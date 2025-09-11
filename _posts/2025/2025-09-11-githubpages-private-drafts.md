---
title: "GitHub Pages 私密文章实现"
date: 2025-09-11 21:32:00 +0800
categories: [Web]
tags: [manuals]     # TAG names should always be lowercase

description: 本文介绍如何在 GitHub Pages 上实现博客草稿和私密文章管理。
---

在使用 GitHub Pages 部署个人主页时，我们需要将对应的仓库（通常命名为 `username.github.io`{: .filepath}）设置为公开（`Public`）。虽然可以通过在仓库中创建 `_drafts/`{: .filepath} 目录来存放草稿文件，从而避免它们出现在生成的网页上。但这些文件依然会对外公开，任何人都能直接在仓库中查看，因此并不能称为私密。

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
