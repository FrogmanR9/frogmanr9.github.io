---
title: "GitHub Pages 私密文章实现"
date: 2025-09-11 21:32:00 +0800
categories: [Web]
tags: [manuals]     # TAG names should always be lowercase

description: 本文介绍如何在 GitHub Pages 上实现博客草稿和私密文章管理。
---

在使用 GitHub Pages 部署个人主页时，我们需要将对应的仓库（通常命名为 `username.github.io`{: .filepath}）设置为公开（`Public`）。虽然可以通过在仓库中创建 `_drafts/`{: .filepath} 目录来存放草稿文件，从而避免它们出现在生成的网页上，但这些文件依然会对外公开，任何人都能直接在仓库中查看，因此并不能称为私密。

我们可以通过 `Git Submodule` 管理草稿内容，实现真正的博客草稿和私密文章管理。

---

## 操作流程

### 创建私有仓库

创建一个新的仓库（例如 `private_drafts`{: .filepath}，后文均以此为例）来管理草稿，注意将其设置为私有（`Private`）仓库。该仓库只用于存放我们的草稿（私密文章）。

### 添加子模块

注意在此之前不要在 `username.github.io`{: .filepath} 仓库中添加 `_drafts/`{: .filepath} 目录。

在本地 `username.github.io`{: .filepath} 仓库中输入以下命令，将 `private_drafts`{: .filepath} 仓库作为子模块添加到 `username.github.io`{: .filepath} 仓库中。
``` shell
git submodule add https://github.com/your-username/private_drafts.git _drafts
git submodule update --init
```

这样，`_drafts/`{: .filepath} 目录就会指向刚刚创建的私有仓库 `private_drafts`{: .filepath}。

GitHub Pages 仍然从 `username.github.io`{: .filepath} 仓库的 main 分支生成网站，但 `private_drafts`{: .filepath} 中的内容会被隔离到子模块中。因为 `private_drafts`{: .filepath} 仓库是私有的，所以草稿内容不会被公开。

需要注意的是，如果今后在新环境中 `git clone` 了 `username.github.io`{: .filepath} 仓库，子模块并不会自动被克隆下来。父仓库只会克隆自己的内容，并记录子模块的 commit 指针。因此需要在新环境中运行：
``` shell
git submodule update --init
```

这条命令会从远程仓库拉取子模块，并初始化它，使得子模块的内容与你在父仓库中提交的指针一致

### 本地查看

GitHub Pages 不会渲染草稿文件，这些文件不会出现在博客的公开页面上。

如果希望查看网页效果，可以在本地运行 `bundle exec jekyll serve --drafts`。

### 更新草稿

在 `private_drafts`{: .filepath} 仓库中改动草稿文件后，本地 `username.github.io`{: .filepath} 仓库的 `_drafts/`{: .filepath} 子模块不会自动更新（这是因为子模块在主仓库中只保存了一个指针，而不是完整文件内容），需要手动同步子模块：
``` shell
cd _drafts
git pull origin main # Assuming the default branch of your drafts repository is main
cd ..
```

此时再运行 `bundle exec jekyll serve --drafts` 就会反映最新的草稿状态。

最后在 VS Code 图形界面提交或输入以下命令同步到远程仓库。
``` shell
git add _drafts
git commit -m "Update drafts submodule"
git push origin main
```

### 在 VS Code 中实现私密文章便捷管理

如果我们在本地 `git clone` 两个仓库，来回切换，极为不便。仍可以仅在本地用 VS Code 打开 `username.github.io`{: .filepath} 仓库，对其 `_drafts/`{: .filepath} 目录直接进行修改（例如添加 `test.md`{: .filepath} 文件，后文均以此为例）。

此时在 `源代码管理` 窗口会存在三个提交窗口：
1. `username.github.io`，main 分支  
这是加入子模块前就有的
2. `username.github.io\_drafts`，main 分支
3. `username.github.io\assets\lib`  
后两个是加入子模块后才出现的。

我们点击后会在下方出现其对应的提交记录。

#### 提交更改

在我们添加 `test.md`{: .filepath} 文件后，窗口 1 和 2 出现相应 `更改`。

我们首先在第二个提交窗口（`_drafts/`{: .filepath} 子模块窗口）中 commit & push。此时可以在 GitHub 的 `private_drafts`{: .filepath} 仓库中看到这个文件。但在 GitHub 的 `username.github.io`{: .filepath} 仓库中点击 `_drafts/`{: .filepath} 目录时，仍会跳转到 `private_drafts`{: .filepath} 仓库的旧提交记录，在这个界面是看不到 `test.md`{: .filepath} 的。

当然，由于 Jekyll 直接读取本地 `_drafts/`{: .filepath} 目录里的文件，我们运行 `bundle exec jekyll serve --drafts` 是能看到 `test.md`{: .filepath} 的。但如果别人 clone 了我们的 `username.github.io`{: .filepath} 仓库，由于此时我们还没有在父仓库提交子模块指针更新，他 clone 下来的 `_drafts/`{: .filepath} 依然会停留在旧的 commit（没有 `test.md`{: .filepath}）。

接下来，我们在第一个提交窗口（父仓库窗口）中 commit & push。此时，父仓库的子模块指针更新，别人再 `git submodule update` 就能同步到带有 `test.md`{: .filepath} 的那次提交。

#### 删除 commit 记录

我们如果希望删除上次对 `_drafts/`{: .filepath} 目录的更改记录，依然来到 `源代码管理` 窗口，右键 `username.github.io\_drafts`，点击 `在集成终端中打开`，输入
``` shell
git reset --soft HEAD~1
git push origin main --force
```

接下来右键 `username.github.io`，点击 `在集成终端中打开`，同样输入
``` shell
git reset --soft HEAD~1
git push origin main --force
```

最后到 `username.github.io`{: .filepath} 仓库中，点击 `Actions`，对目标工作流 `Delete workflow run` 即可。

注：最后一步可选，删除 workflow run 只影响`Actions` 界面的整洁度和日志可见性，不影响网站。

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
