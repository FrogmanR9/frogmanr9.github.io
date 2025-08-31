---
title: "gem5 分支预测模块简介"
date: 2025-08-29 22:54:00 +0800
categories: [CPU, Branch Prediction Unit (BPU)]
tags: [c-model, cpp, gem5, notes]     # TAG names should always be lowercase

description: 本文简要介绍 gem5 的分支预测模块，为在 gem5 上实现自定义的分支预测器做准备。
math: true
---

gem5 是一个模块化的、基于离散事件驱动的计算机系统模拟平台，相关资料可以在 [官网](https://www.gem5.org) 找到，不在此赘述。

本文使用 `gem5 version 22.0.0.0`，基于 gem5 O3CPU（Out of Order）模型，后文不再强调。O3CPU 的简介可见 [O3CPU overview](https://www.gem5.org/documentation/general_docs/cpu_models/O3CPU)。

预测器相关代码路径：`src/cpu/pred/`{: .filepath}；  
O3CPU 相关代码路径：`src/cpu/o3/`{: .filepath}。

---

## gem5 预测器接口与 time buffer

gem5 采用耦合式前端，其分支预测流水线与取指流水线紧耦合。

``` cpp
/** BPredUnit. */
branch_prediction::BPredUnit *branchPred;
```
{: file='fetch.hh'}

处理器其他模块要想与预测器通信，需要先通过 time buffer 将信息传递给 fetch 模块。

这里简单介绍一下 O3CPU 使用的 time buffer，在 `comm.hh`{: .filepath} 中定义了两类 time buffer，分别是
1. 前向通信，主要结构包括 `struct FetchStruct`（from fetch to decode）、`struct IEWStruct`（from IEW to commit）等。前向流是流水线数据，承载的是指令流本身，逐级传递。而各级的带宽、延迟不同，因此用不同的 struct 来精确描述每一级传输的“一组指令 bundle”。
2. 后向通信，由 `struct TimeStruct` 承担，其中定义了 `struct DecodeComm`、`struct IewComm` 等结构体。后向信息不是指令流，而是一些全局控制信号。这些控制信息在硬件里通常通过广播总线/反馈总线传递给多个 stage，而不是逐级传递。因此用一个大的 struct 把所有后向信息打包，每个 stage 自己读取和写入相关控制信号。

每个周期都会推进 time buffer：
``` cpp
timeBuffer.advance();

fetchQueue.advance();
decodeQueue.advance();
renameQueue.advance();
iewQueue.advance();
```
{: file='cpu.cc'}

以 iew stage 为例，会设置传播延时，然后对相关的 time buffer 进行读取和写入，读取的事实上就是源模块在延时之前发出的信息，从而模拟硬件行为。
``` cpp
void
IEW::setTimeBuffer(TimeBuffer<TimeStruct> *tb_ptr)
{
    timeBuffer = tb_ptr;

    // Setup wire to read information from time buffer, from commit.
    fromCommit = timeBuffer->getWire(-commitToIEWDelay);

    // Setup wire to write information back to previous stages.
    toRename = timeBuffer->getWire(0);

    toFetch = timeBuffer->getWire(0);

    // Instruction queue also needs main time buffer.
    instQueue.setTimeBuffer(tb_ptr);
}

void
IEW::setRenameQueue(TimeBuffer<RenameStruct> *rq_ptr)
{
    renameQueue = rq_ptr;

    // Setup wire to read information from rename queue.
    fromRename = renameQueue->getWire(-renameToIEWDelay);
}

void
IEW::setIEWQueue(TimeBuffer<IEWStruct> *iq_ptr)
{
    iewQueue = iq_ptr;

    // Setup wire to write instructions to commit.
    toCommit = iewQueue->getWire(0);
}
```

需要注意的是，这里的 `toRename` 和 `toFetch` 写的是同一个 `TimeStruct`，命名不同只是为了直观，我们完全可以统一成一个 `toPrev`。事实上 commit stage 就是这么做的，它只定义了一个反向传播的 `toIEW = timeBuffer->getWire(0);`，然而不仅仅是 iew stage，decode、fetch 都需要 commitInfo。

---

## 预测器 Checkpoint、历史维护及相关函数

本节通过学习以下预测器总结：
* `BPredUnit`（`bpred_unit.hh and .cc`{: .filepath}）：其他预测器（直接或间接）以之为基类；
* `BiModeBP`（`bi_mode.hh and .cc`{: .filepath}）：直接继承自 BPredUnit，学习各个虚函数的作用；
* `TAGE`（`tage.hh and .cc`{: .filepath}）：进一步明确历史管理的实现方式。

### Checkpoint

主要结构：
``` cpp
    struct PredictorHistory
    {
        /**
         * Makes a predictor history struct that contains any
         * information needed to update the predictor, BTB, and RAS.
         */
        ...

        /** The sequence number for the predictor history entry. */
        InstSeqNum seqNum;

        /** The PC associated with the sequence number. */
        Addr pc;

        /** Pointer to the history object passed back from the branch
         * predictor.  It is used to update or restore state of the
         * branch predictor.
         */
        void *bpHistory = nullptr;

        void *indirectHistory = nullptr;

        /** The RAS target (only valid if a return). */
        std::unique_ptr<PCStateBase> RASTarget;

        /** The RAS index of the instruction (only valid if a call). */
        unsigned RASIndex = 0;

        /** The thread id. */
        ThreadID tid;

        /** Whether or not it was predicted taken. */
        bool predTaken;

        /** Whether or not the RAS was used. */
        bool usedRAS = false;

        /* Whether or not the RAS was pushed */
        bool pushedRAS = false;

        /** Whether or not the instruction was a call. */
        bool wasCall = false;

        /** Whether or not the instruction was a return. */
        bool wasReturn = false;

        /** Wether this instruction was an indirect branch */
        bool wasIndirect = false;

        /** Target of the branch. First it is predicted, and fixed later
         *  if necessary
         */
        Addr target = MaxAddr;

        /** The branch instrction */
        const StaticInstPtr inst;
    };

    typedef std::deque<PredictorHistory> History;

    /**
     * The per-thread predictor history. This is used to update the predictor
     * as instructions are committed, or restore it to the proper state after
     * a squash.
     */
    std::vector<History> predHist;
```
{: file='bpred_unit.cc'}

`predHist` 在调用 `predict` 函数时 push。
``` cpp
bool
BPredUnit::predict(const StaticInstPtr &inst, const InstSeqNum &seqNum,
                   PCStateBase &pc, ThreadID tid)
    PredictorHistory predict_record(seqNum, pc.instAddr(), pred_taken,
                                    bp_history, indirect_history, tid, inst);
    predHist[tid].push_front(predict_record);
```
{: file='bpred_unit.cc'}

在指令提交后调用 `update` 函数 pop_back。
``` cpp
void
BPredUnit::update(const InstSeqNum &done_sn, ThreadID tid)
    while (!predHist[tid].empty() &&
           predHist[tid].back().seqNum <= done_sn) {

        predHist[tid].pop_back();
```
{: file='bpred_unit.cc'}

在预测器 squash 时 pop_front。
``` cpp
void
BPredUnit::squash(const InstSeqNum &squashed_sn, ThreadID tid)
    History &pred_hist = predHist[tid];

    while (!pred_hist.empty() &&
           pred_hist.front().seqNum > squashed_sn) {
        pred_hist.pop_front();
```
{: file='bpred_unit.cc'}

### 历史维护及相关函数

在具体实现中有两类历史：
1. 预测器维护的历史（记作 bp_hist）：在建模中作为预测器的成员；
2. 各分支指令维护的历史（记作 bp_history）：在建模中由分支指令携带。

每一条分支指令对应的 `struct PredictorHistory` 中 `bpHistory` 指向的就是该分支维护的历史。在每次预测时会 new 一个预测器历史对象（各预测期根据需要自己定义）并将 `bpHistory` 指向它；因 commit 或 squash 引起的指令 Checkpoint（`PredictorHistory`）的销毁（pop_back 或 pop_front）会紧随这个历史对象的 delete 之后进行。

每次对一条分支指令进行预测时，会将预测器维护的历史赋给该指令维护的历史，然后根据本次预测结果对 **预测器维护的历史** 进行推测性更新；当发生分支预测错误时，会 flush 掉流水线中该错误预测分支之后（**不含**）的指令，然后将这条错误预测分支维护的历史赋给预测器维护的历史，再根据这条指令的正确方向和目标地址对预测器维护的历史进行更新。

具体来说，在 `BPredUnit` 类中有五大 **纯虚函数**（注意不要与其他重载函数混淆），是需要继承它的子类重写的。

``` cpp
void uncondBranch(ThreadID tid, Addr PC, void * &bp_history) override;
```
无条件分支指令会从 `predict` 函数中进入 `uncondBranch` 函数，在这个函数中需要 new 一个预测器历史对象（作为该指令维护的历史），将预测器维护的历史赋给它，然后将本次预测记录为跳转，并据此对预测器维护的历史进行推测性更新。

``` cpp
void squash(ThreadID tid, void *bp_history) override;
```
对预测器 squash 时在相关函数内部调用。需要将该指令维护的历史 delete 掉。

``` cpp
bool lookup(ThreadID tid, Addr branch_addr, void * &bp_history) override;
```
条件分支指令会从 `predict` 函数中进入 `lookup` 函数，在这个函数中需要 new 一个预测器历史对象（作为该指令维护的历史），将预测器维护的历史赋给它，然后进行分支预测并记录，最后据此对预测器维护的历史进行推测性更新。

``` cpp
void btbUpdate(ThreadID tid, Addr branch_addr, void * &bp_history) override;
```
发生 BTB miss 时调用。这种情况下，即使预测器成功预测了分支方向，也无法给出跳转目标地址。因此，这个分支会被当作 not taken 处理，并据此对推测性更新的预测器维护的历史进行修正。

``` cpp
void update(ThreadID tid, Addr branch_addr, bool taken, void *bp_history,
  bool squashed, const StaticInstPtr & inst, Addr corrTarget) override;
```
当 commit 阶段没有 squash 信号且有指令提交，会在相关函数内部调用，传入的 squashed 为 false。我们会对预测器的部件进行更新，并且将该指令维护的历史 delete 掉。

当发生分支预测错误，会在相关函数内部调用，传入的 squashed 为 true。我们会将这条错误预测分支维护的历史赋给预测器维护的历史，再根据这条指令的正确方向和目标地址对预测器维护的历史进行更新。

---

## 预测器其他主要函数及分支预测正确、错误处理流程

### doneSeqNum

相关代码（有多处）：

``` cpp
// (1)
void
Commit::squashAll(ThreadID tid)
    InstSeqNum squashed_inst = rob->isEmpty(tid) ?
        lastCommitedSeqNum[tid] : rob->readHeadInst(tid)->seqNum - 1;
    toIEW->commitInfo[tid].doneSeqNum = squashed_inst;

// (2)
void
Commit::commit()
            InstSeqNum squashed_inst = fromIEW->squashedSeqNum[tid];
            if (fromIEW->includeSquashInst[tid]) {
                squashed_inst--;
            }
            toIEW->commitInfo[tid].doneSeqNum = squashed_inst;

// (3)
void
Commit::commitInsts()
            // Try to commit the head instruction.
            bool commit_success = commitHead(head_inst, num_committed);
            if (commit_success) {
                // Set the doneSeqNum to the youngest committed instruction.
                toIEW->commitInfo[tid].doneSeqNum = head_inst->seqNum;

// (4)
void
Decode::squash(const DynInstPtr &inst, ThreadID tid)
    toFetch->decodeInfo[tid].doneSeqNum = inst->seqNum;
```

**在分支预测模块**，它的使用主要出于两种目的：
1. 作为 youngest committed inst sn：指令 commit 了，把这条指令之前（**含**）各指令的相关 Checkpoint 销毁；
2. 作为 squashed sn：squash 了，把这条指令之后（**不含**）各指令的相关 Checkpoint 销毁

### Fetch::checkSignalsAndUpdate

``` cpp
bool
Fetch::checkSignalsAndUpdate(ThreadID tid)

    // Check squash signals from commit.
    if (fromCommit->commitInfo[tid].squash) {

        DPRINTF(Fetch, "[tid:%i] Squashing instructions due to squash "
                "from commit.\n",tid);
        // In any case, squash.
        squash(*fromCommit->commitInfo[tid].pc,
               fromCommit->commitInfo[tid].doneSeqNum,
               fromCommit->commitInfo[tid].squashInst, tid);

        // If it was a branch mispredict on a control instruction, update the
        // branch predictor with that instruction, otherwise just kill the
        // invalid state we generated in after sequence number
        if (fromCommit->commitInfo[tid].mispredictInst &&
            fromCommit->commitInfo[tid].mispredictInst->isControl()) {
            branchPred->squash(fromCommit->commitInfo[tid].doneSeqNum,
                    *fromCommit->commitInfo[tid].pc,
                    fromCommit->commitInfo[tid].branchTaken, tid);
        } else {
            branchPred->squash(fromCommit->commitInfo[tid].doneSeqNum,
                              tid);
        }

        return true;
    } else if (fromCommit->commitInfo[tid].doneSeqNum) {
        // Update the branch predictor if it wasn't a squashed instruction
        // that was broadcasted.
        branchPred->update(fromCommit->commitInfo[tid].doneSeqNum, tid);
    }

    // Check squash signals from decode.
    if (fromDecode->decodeInfo[tid].squash) {
        DPRINTF(Fetch, "[tid:%i] Squashing instructions due to squash "
                "from decode.\n",tid);

        // Update the branch predictor.
        if (fromDecode->decodeInfo[tid].branchMispredict) {
            branchPred->squash(fromDecode->decodeInfo[tid].doneSeqNum,
                    *fromDecode->decodeInfo[tid].nextPC,
                    fromDecode->decodeInfo[tid].branchTaken, tid);
        } else {
            branchPred->squash(fromDecode->decodeInfo[tid].doneSeqNum,
                              tid);
        }

        if (fetchStatus[tid] != Squashing) {

            DPRINTF(Fetch, "Squashing from decode with PC = %s\n",
                *fromDecode->decodeInfo[tid].nextPC);
            // Squash unless we're already squashing
            squashFromDecode(*fromDecode->decodeInfo[tid].nextPC,
                             fromDecode->decodeInfo[tid].squashInst,
                             fromDecode->decodeInfo[tid].doneSeqNum,
                             tid);

            return true;
        }
    }
```

先检查 commit 的信号。如果 squash，分是否由分支预测错误引起处理并 return；否则如果有指令提交，更新预测器。

再检查 decode 的信号。如果 squash，分是否由分支预测错误引起处理。

### squash

在 `bpred_unit.hh`{: .filepath} 中共有三个 `squash` 函数。调用处如下：
``` cpp
bool
Fetch::checkSignalsAndUpdate(ThreadID tid)

        if (fromCommit->commitInfo[tid].mispredictInst &&
            fromCommit->commitInfo[tid].mispredictInst->isControl()) {
            branchPred->squash(fromCommit->commitInfo[tid].doneSeqNum,
                    *fromCommit->commitInfo[tid].pc,
                    fromCommit->commitInfo[tid].branchTaken, tid);
        } else {
            branchPred->squash(fromCommit->commitInfo[tid].doneSeqNum,
                              tid);
        }

        if (fromDecode->decodeInfo[tid].branchMispredict) {
            branchPred->squash(fromDecode->decodeInfo[tid].doneSeqNum,
                    *fromDecode->decodeInfo[tid].nextPC,
                    fromDecode->decodeInfo[tid].branchTaken, tid);
        } else {
            branchPred->squash(fromDecode->decodeInfo[tid].doneSeqNum,
                              tid);
        }
```

第一个 `squash`：是分支预测错误引起的 squash，需要 squash 预测器并更新预测器。中间会调用第二个 `squash`：
``` cpp
    // Squash All Branches AFTER this mispredicted branch
    squash(squashed_sn, tid);
```

第二个 `squash`：负责 squash 预测器，kill the invalid state we generated in after sequence number。

`brepd_unit` 中还有一个 `squash`，就是那个需要重写的纯虚函数，在第二个 `squash` 中调用：
``` cpp
    while (!pred_hist.empty() &&
           pred_hist.front().seqNum > squashed_sn) {
        // This call should delete the bpHistory.
        squash(tid, pred_hist.front().bpHistory);
```

这里的 doneSeqNum 是不被 flush 掉的最新的指令。

每当我们要 squash 这个预测器，我们会把预测器维护的历史逐渐回退，并把 flush 掉的指令维护的历史 delete 掉。最后一次 `bp_hist = Ri->hist;` 会使得预测器维护的历史变成 doneSeqNum + 1 的那个被 flush 掉的指令进行分支预测、改动预测器维护历史之前的状态，也就是 doneSeqNum 这条指令改动过后的状态。

第二个 `squash` 会 `pred_hist.pop_front();`，最终 `pred_hist` 中最新的是 doneSeqNum 对应的指令。

### update

在 `bpred_unit.hh`{: .filepath} 中共有两个 `update` 函数。调用处如下：
``` cpp
bool
Fetch::checkSignalsAndUpdate(ThreadID tid)
    if (fromCommit->commitInfo[tid].squash) {
    } else if (fromCommit->commitInfo[tid].doneSeqNum) {
        // Update the branch predictor if it wasn't a squashed instruction
        // that was broadcasted.
        branchPred->update(fromCommit->commitInfo[tid].doneSeqNum, tid);
    }
```

如果 commit 阶段没有 squash 信号且有指令提交，调用 `update`，tell the branch predictor to commit any updates until the given sequence number。

``` cpp
    while (!predHist[tid].empty() &&
           predHist[tid].back().seqNum <= done_sn) {
        // Update the branch predictor with the correct results.
        update(tid, predHist[tid].back().pc,
                    predHist[tid].back().predTaken,
                    predHist[tid].back().bpHistory, false,
                    predHist[tid].back().inst,
                    predHist[tid].back().target);
```

这里面调用的就是那个虚函数，一直到这个 doneSeqNum 为止。注意它的 squashed 为 false，我们会对预测器的部件进行更新，并且 `delete Ri;`。

上面是 squashed 为 false 的情况，下面是为 true 的情况：
``` cpp
void
BPredUnit::squash(const InstSeqNum &squashed_sn,
                  const PCStateBase &corr_target,
                  bool actually_taken, ThreadID tid)
        update(tid, (*hist_it).pc, actually_taken,
               pred_hist.front().bpHistory, true, pred_hist.front().inst,
               corr_target.instAddr());
```

这里的 `update` 在 `squash(squashed_sn, tid);` 之后调用，此时将 `pred_hist.front()`（也就是 doneSeqNum 对应的指令） 维护的历史赋给预测器维护的历史，然后调用 `update_hist` 对预测器历史进行更新。

我们重写的虚函数里 `if (squashed)` 的情况不需要 delete，因为走到这里的其实是 doneSeqNum 对应的指令，是我们要保的。

### 分支预测正确、错误处理流程

一条分支指令在 decode stage 发现预测错误，或在 exe stage 真正计算出结果，此时在它之后可能会有很多分支指令已经进入流水线、进行了分支预测。

当这条分支指令没有被 flush 掉且预测正确，commit stage 会将该分支同周期提交的那组指令中最新指令的编号传给 fetch，fetch 会调用 `update` 函数，后者调用 `update(squashed = false)`，对预测器部件进行更新，并销毁 Checkpoint。

当这条分支指令不是因预测错误而被 flush 掉，会调用 `squash` 函数，squash 预测器，并逐渐回退预测器维护的历史，最后预测器维护的历史变成 doneSeqNum + 1 的那个被 flush 掉的指令进行分支预测、改动预测器维护历史之前的状态，也就是 doneSeqNum 这条指令改动过后的状态。

当这条指令分支预测错误且没有在处理前被 flush 掉，在上面的流程之后，会调用 `update(squashed = true)`，将 `pred_hist.front()`（也就是 doneSeqNum 对应的指令） 维护的历史赋给预测器维护的历史，然后 `update_hist` 对预测器历史进行更新。

---

## 预测器性能评估和相关统计信息

作者参考了 [gem5使用指南](https://enoch2090-blog.vercel.app/article/gem5-manual) 中分享的脚本 `benchmark.py`{: .filepath}，在初步评估预测器性能时考察 IPC、条件分支预测正确率和 MPKI（Mispredictions Per Kilo Instructions）三个指标。这三个指标均通过从 `m5out/stats.txt`{: .filepath} 中读取数据来计算。
``` python
ipc = stats_dict["system.cpu.ipc"]
incorr = stats_dict["system.cpu.branchPred.condIncorrect"]
totbr = stats_dict["system.cpu.branchPred.condPredicted"]
mispred = stats_dict["system.cpu.commit.branchMispredicts"]
ninst = stats_dict["system.cpu.thread_0.numInsts"]

def print_metric(name, value, formula):
    print(f"- {name:<10} {value:<8} {formula}")

print_metric("IPC", f"{ipc:.4f}", "(numInsts / cycles)")
print_metric("Accuracy", f"{(1 - incorr / totbr) * 100:.2f}%", "(1 - condIncorrect / condPredicted)")
print_metric("MPKI", f"{(mispred / ninst * 1000):.4f}", "(branchMispredicts / numInsts * 1000)")
```

即

$$
\begin{align}
\text{IPC} &= \frac{\text{numInsts}}{\text{cycles}} \\
\text{Accuracy} &= \left( 1 - \frac{\text{condIncorrect}}{\text{condPredicted}} \right) \times 100 \\
\text{MPKI} &= \frac{\text{branchMispredicts}}{\text{numInsts}} \times 1000 \\
\end{align}
$$

本节将对使用的 `numInsts`、`condPredicted`、`condIncorrect` 和 `branchMispredicts` 四个统计数据进行追踪和分析。

### numInsts

相关代码：
``` cpp
void CPU::instDone(ThreadID tid, const DynInstPtr& inst)
    if (!inst->isMicroop() || inst->isLastMicroop()) {
        thread[tid]->numInst++;
        thread[tid]->threadStats.numInsts++;

void
Commit::updateComInstStats(const DynInstPtr &inst)
    if (!inst->isNop() && !inst->isInstPrefetch()) {
        cpu->instDone(tid, inst);
    }

bool
Commit::commitHead(const DynInstPtr &head_inst, unsigned inst_num)
    updateComInstStats(head_inst);
```

所以这个是实实在在的 commit 的指令数。

### condPredicted

相关代码：
``` cpp
bool
BPredUnit::predict(const StaticInstPtr &inst, const InstSeqNum &seqNum,
                   PCStateBase &pc, ThreadID tid)
    if (inst->isUncondCtrl()) {
    } else {
        ++stats.condPredicted;
        pred_taken = lookup(tid, pc.instAddr(), bp_history);
        DPRINTF(Branch, "[tid:%i] [sn:%llu] "
                "Branch predictor predicted %i for PC %s\n",
                tid, seqNum,  pred_taken, pc);

bool
Fetch::lookupAndUpdateNextPC(const DynInstPtr &inst, PCStateBase &next_pc)
    predict_taken = branchPred->predict(inst->staticInst, inst->seqNum,
                                        next_pc, tid);
```

这是调用 `predict` 函数的条件分支数。

### condIncorrect

直接来自于
``` cpp
void
BPredUnit::squash(const InstSeqNum &squashed_sn,
                  const PCStateBase &corr_target,
                  bool actually_taken, ThreadID tid)
{
    // Now that we know that a branch was mispredicted, we need to undo
    // all the branches that have been seen up until this branch and
    // fix up everything.
    // NOTE: This should be call conceivably in 2 scenarios:
    // (1) After an branch is executed, it updates its status in the ROB
    //     The commit stage then checks the ROB update and sends a signal to
    //     the fetch stage to squash history after the mispredict
    // (2) In the decode stage, you can find out early if a unconditional
    //     PC-relative, branch was predicted incorrectly. If so, a signal
    //     to the fetch stage is sent to squash history after the mispredict
    ++stats.condIncorrect;
```

调用这个 `squash` 函数的是 `Fetch::checkSignalsAndUpdate` 函数，有两处：
``` cpp
bool
Fetch::checkSignalsAndUpdate(ThreadID tid)
// 我们在这里记为（1）
        if (fromCommit->commitInfo[tid].mispredictInst &&
            fromCommit->commitInfo[tid].mispredictInst->isControl()) {
            branchPred->squash(fromCommit->commitInfo[tid].doneSeqNum,
                    *fromCommit->commitInfo[tid].pc,
                    fromCommit->commitInfo[tid].branchTaken, tid);
// （2）
        if (fromDecode->decodeInfo[tid].branchMispredict) {
            branchPred->squash(fromDecode->decodeInfo[tid].doneSeqNum,
                    *fromDecode->decodeInfo[tid].nextPC,
                    fromDecode->decodeInfo[tid].branchTaken, tid);
```

#### （1）

相关代码：
``` cpp
void
Commit::commit()
            toIEW->commitInfo[tid].mispredictInst =
                fromIEW->mispredictInst[tid];

void
IEW::squashDueToBranch(const DynInstPtr& inst, ThreadID tid)
toCommit->mispredictInst[tid] = inst;

void
IEW::executeInsts()
            if (inst->mispredicted() && !loadNotExecuted) {
                squashDueToBranch(inst, tid);

    /** Returns whether the instruction mispredicted. */
    bool
    mispredicted()
    {
        std::unique_ptr<PCStateBase> next_pc(pc->clone());
        staticInst->advancePC(*next_pc);
        return *next_pc != *predPC;
    }
```

所以是从 exe 到 commit 再到 fetch（pred）。

#### （2）

相关代码：
``` cpp
void
Decode::squash(const DynInstPtr &inst, ThreadID tid)
    toFetch->decodeInfo[tid].branchMispredict = true;

void
Decode::decodeInsts(ThreadID tid)
// 如果该指令不是控制流指令（!inst->isControl()），但是预测为分支
        if (inst->readPredTaken() && !inst->isControl()) {
            squash(inst, inst->threadNumber);
// 对于直接控制流分支（isDirectCtrl()），代码会验证分支的目标地址是否正确
        if (inst->isDirectCtrl() &&
           (inst->isUncondCtrl() || inst->readPredTaken()))
            std::unique_ptr<PCStateBase> target = inst->branchTarget();
            if (*target != inst->readPredTarg()) {
                ++stats.branchMispred;
                squash(inst, inst->threadNumber);
```

控制流指令（Control Flow Instructions）通常指的是分支指令。

### branchMispredicts

相关代码：
``` cpp
void
Commit::commit()
            toIEW->commitInfo[tid].mispredictInst =
                fromIEW->mispredictInst[tid];
            toIEW->commitInfo[tid].branchTaken =
                fromIEW->branchTaken[tid];
            toIEW->commitInfo[tid].squashInst =
                                    rob->findInst(tid, squashed_inst);
            if (toIEW->commitInfo[tid].mispredictInst) {
                if (toIEW->commitInfo[tid].mispredictInst->isUncondCtrl()) {
                     toIEW->commitInfo[tid].branchTaken = true;
                }
                ++stats.branchMispredicts;
            }
```

注意无条件分支没有 break 出去。

### 总结

numInsts：退休一条指令加一。

condPredicted：fetch 调一次 BPredUnit::predict，且为条件分支，加一。

condIncorrect：  
1. decode 阶段发现  
（1）将非分支指令预测为分支跳转  
or  
（2）直接分支 且 无条件或预测为跳转，目标地址错误  
则向fetch传递branchMispredict信息；
2. exe 阶段发现指令 `*next_pc != *predPC`  
则向 commit 传递 mispredictInst 信息，commit 阶段再向 fetch 传递 mispredictInst 信息

最后 fetch 调用 `BPredUnit::squash`，condIncorrect 加一。

branchMispredicts：  
exe 阶段发现指令 `*next_pc != *predPC`，则向 commit 传递 mispredictInst 信息  
commit 阶段 branchMispredicts 加一。

实例：
``` text
system.cpu.branchPred.condIncorrect 51 # Number of conditional branches incorrect (Count)
system.cpu.decode.branchMispred 13 # Number of times decode detected a branch misprediction (Count)
system.cpu.iew.branchMispredicts 48 # Number of branch mispredicts detected at execute (Count)
system.cpu.commit.branchMispredicts 38 # The number of times a branch was mispredicted (Count)
```
{: file='m5out/stats.txt'}

可以看到

$$
\text{system.cpu.branchPred.condIncorrect} = \text{system.cpu.decode.branchMispred} + \text{system.cpu.commit.branchMispredicts}
$$

需注意的是，直接、间接分支和条件、非条件分支的概念是正交的，统计的条件分支指令个数和 condIncorrect 个数中既有直接指令也有间接指令。

---

Everyone is welcome to reach out and exchange ideas.



Please credit the source as `Frogman's Blog (https://frogmanr9.github.io)` and include the link to this article (`{{ page.url | absolute_url }}`). Thank you.



<!-- Font Awesome CSS for icons -->
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" 
      integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" 
      crossorigin="anonymous">

<!-- Busuanzi visitor counter script -->
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<span>
  This page has 
  <span id="busuanzi_value_page_pv">
    <!-- Loading spinner before data is fetched -->
    <i class="fa fa-spinner fa-spin"></i>
  </span> views.
</span>
