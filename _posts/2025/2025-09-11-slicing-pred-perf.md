---
title: "切片后的分支预测性能指标计算：IPC、Accuracy 与 MPKI"
date: 2025-09-11 13:20:00 +0800
categories: [CPU, Branch Prediction Unit (BPU)]
tags: [c-model, notes, perf-eval]     # TAG names should always be lowercase

description: 本文介绍如何利用切片统计数据计算整体分支预测性能指标。
math: true
---

> 本文源自师兄指点。
{: .prompt-info }

我们在 [预测器性能评估和相关统计信息](https://frogmanr9.github.io/posts/gem5-bpu-intro/#预测器性能评估和相关统计信息) 中介绍了三个指标（`IPC`、`Accuracy`、`MPKI`），用于对分支预测器的性能进行初步评估。

当我们运行较大的测试程序集（如 SPEC2006、SPEC2017）时，经常采用切片的方法。我们很容易得到各个切片的统计数据，但如何评估整体的分支预测性能呢？

对于每个切片 $\text{Slice}_i$，我们有权重

$$
w_i = \frac{\text{numInsts}_i}{\text{numInsts}_{\text{total}}}
$$

满足

$$
\sum\limits_{i} w_i = 1
$$

下面将根据各切片权重和统计数据计算整体性能指标。对推导没有兴趣的可以直接看 [结论](https://frogmanr9.github.io/posts/slicing-pred-perf/#summary)。

---

## IPC

$$
\begin{align*}
    \text{IPC}_{\text{total}} &= \frac{\text{numInsts}_{\text{total}}}{\text{numCycles}_{\text{total}}} \\
    &= \frac{\text{numInsts}_{\text{total}}}{\sum\limits_{i} \text{numInsts}_{\text{total}} \cdot w_i \cdot \text{CPI}_i} \\
    &= \frac{1}{\sum\limits_{i} w_i \cdot \text{CPI}_i}
\end{align*}
$$

---

## Accuracy

我们分别计算条件分支和所有分支指令的预测正确率。

$$
\begin{align*}
    \text{AccuracyCond}_{\text{total}} &= \left( 1 - \frac{\text{numCondIncorrect}_{\text{total}}}{\text{numCondPredicted}_{\text{total}}} \right) \cdot 100\% \\
    &= \left( 1 - \frac{\sum\limits_{i} \text{numCondPredicted}_{\text{total}} \cdot w_i \cdot \frac{\text{numCondIncorrect}_i}{\text{numCondPredicted}_i}}{\text{numCondPredicted}_{\text{total}}} \right) \cdot 100\% \\
    &= \left( 1 - \sum\limits_{i} w_i \cdot \frac{\text{numCondIncorrect}_i}{\text{numCondPredicted}_i} \right) \cdot 100\% \\
    &= \sum\limits_{i} w_i \cdot \left( \left( 1 - \frac{\text{numCondIncorrect}_i}{\text{numCondPredicted}_i} \right) \cdot 100\% \right) \\
    &= \sum\limits_{i} w_i \cdot \text{AccuracyCond}_i
\end{align*}
$$

$$
\begin{align*}
    \text{AccuracyBr}_{\text{total}} &= \left( 1 - \frac{\text{numBrIncorrect}_{\text{total}}}{\text{numBrPredicted}_{\text{total}}} \right) \cdot 100\% \\
    &= \left( 1 - \frac{\sum\limits_{i} \text{numBrPredicted}_{\text{total}} \cdot w_i \cdot \frac{\text{numBrIncorrect}_i}{\text{numBrPredicted}_i}}{\text{numBrPredicted}_{\text{total}}} \right) \cdot 100\% \\
    &= \left( 1 - \sum\limits_{i} w_i \cdot \frac{\text{numBrIncorrect}_i}{\text{numBrPredicted}_i} \right) \cdot 100\% \\
    &= \sum\limits_{i} w_i \cdot \left( \left( 1 - \frac{\text{numBrIncorrect}_i}{\text{numBrPredicted}_i} \right) \cdot 100\% \right) \\
    &= \sum\limits_{i} w_i \cdot \text{AccuracyBr}_i
\end{align*}
$$

---

## MPKI

$$
\begin{align*}
    \text{MPKI}_{\text{total}} &= \frac{\text{numBrMispredicted}}{\text{numInsts}_{\text{total}}} \cdot 1000 \\
    &= \frac{\sum\limits_{i} \text{numInsts}_{\text{total}} \cdot w_i \cdot \left(\frac{\text{numBrMispredicted}_i}{\text{numInsts}_i} \cdot 1000 \right)}{\text{numInsts}_{\text{total}}} \\
    &= \sum\limits_{i} w_i \cdot \text{MPKI}_i
\end{align*}
$$

---

## Summary

$$
\begin{align}
    \text{IPC}_{\text{total}} &= \frac{1}{\sum\limits_{i} w_i \cdot \text{CPI}_i} \\
    \text{AccuracyCond}_{\text{total}} &= \sum\limits_{i} w_i \cdot \text{AccuracyCond}_i \\
    \text{AccuracyBr}_{\text{total}} &= \sum\limits_{i} w_i \cdot \text{AccuracyBr}_i \\
    \text{MPKI}_{\text{total}} &= \sum\limits_{i} w_i \cdot \text{MPKI}_i
\end{align}
$$

在实际性能评估过程中还会遇到数据选取的问题。例如一个切片中预测错误的分支指令数，decode 阶段和 commit 阶段均有统计，是选取它们之和，还是选取 commit 阶段的统计数据，即是否将被 flush 掉的错误预测分支计入，需要我们根据实际需要审慎考量。这也是本文使用 `numBrIncorrect`、`numBrMispredicted` 等不同名称的原因。

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
