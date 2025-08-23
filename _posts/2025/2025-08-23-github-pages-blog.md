---
title: 使用 Github Pages 搭建个人主页
date: 2025-08-23 12:30:00 +0800
categories: [About]
tags: [guidelines, jekyll, site]     # TAG names should always be lowercase

description: 本文介绍使用 Github Pages + Jekyll Chirpy 搭建个人主页的流程和注意事项。需注意的是，本文不涉及详细的个性化配置。
---

## 环境

本文采用 Windows 11 + VS Code。

---

## 操作流程

### 参考资料

* [【避坑篇】使用Github Pages搭建个人主页or博客网站【上】](https://zhuanlan.zhihu.com/p/641525444)
* [【快速部署+客制化】Github Pages+Jekyll Chirpy 速搭个人主页](https://zhuanlan.zhihu.com/p/695291923)
* [Chirpy notes](https://chirpy.cotes.page)
* [Jekyll docs](https://jekyllcn.com/docs/home/)
* [GitHub Pages documentation](https://docs.github.com/en/pages)
* [Jekyll SEO tag](https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/usage.md)

### 安装 Ruby

1. 在 [官网](https://rubyinstaller.org/downloads/) 下载最新版 WITH DEVKIT 的 Ruby。

2. 下载完毕打开 ridk install，在跳出的 CMD 中输入 3（选择“MSYS2 and MINGW development tool chain”）。

### 安装 Jekyll

1. 打开 CMD，安装 Jekyll
``` shell
gem install jekyll bundler
```

2. 查看是否成功安装
``` shell
jekyll -v
```

### 新建 Github 仓库

1. 有两种选择：
* 对于新手而言，建议使用 [chirpy-starter](https://github.com/cotes2020/chirpy-starter)，缺点是能做的改动较少；
* 如果对 Jekyll 比较熟悉或希望做较大改动，可以使用 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)。

这里介绍前一种。使用前一种时也可以将希望修改的文件从 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 中迁移过来进行改动。

2. 点击 `Use this template`，`Create a new repository`，仓库命名为 `username.github.io`{: .filepath}。注意区分 Github 的 name 和 username，如果 username 中有大写则用小写。

3. 在新建仓库中点击 `Settings`，在 `Pages` 中将 Source 修改为 `Github Action`。

4. 可以将 `_config.yml`{: .filepath} 文件中的 url 改为自己的网址，此时从浏览器进入即可看到原始的网站了。

### 本地部署和调试

1. 打开 CMD，到目标目录，从 Github 上克隆刚刚创建的仓库到本地。

2. 打开 VS Code，到该仓库的根目录下，在终端输入
``` shell
bundle
```

3. 在 VS Code 终端输入
``` shell
bundle exec jekyll s
```
此时可以看到以下信息，从浏览器进入该网址即可实现本地预览。
> Server address: http://127.0.0.1:4000/  

### 网站基本配置

1. 在 `_config.yml`{: .filepath} 文件中进行配置，包括但不限于：
* url
* avatar：侧边栏头像，可选择本地路径或链接。如选择本地路径，将图片放在 `assets/img/`{: .filepath} 中并在 `_config.yml`{: .filepath} 中填写路径
* title: 侧边栏名称
* timezone: 中国输入 Asia/Shanghai
* 注意 `social` 下的 `links` 至少要有一个链接，且注意格式（`- https://...`）

2. `_data/contact.yml`{: .filepath} 文件配置网站侧边栏底部诸 `contact` 按钮，对不想要的可以直接注释掉。

3. 在 `_tabs/about.md`{: .filepath} 文件中配置 `ABOUT` 页信息。

### 部署个人主页

把本地修改 commit 并提交到 Github 远程仓库上，可以触发 `Github Action` 的 `Build and deployment` 工作流进行部署。如果没有自动触发，可以手动点击 `Build and Deploy` 进行部署。

---

## 其他配置

### 撰写文章

参见 [Writing a New Post](https://chirpy.cotes.page/posts/write-a-new-post/)。

注意文件名 `YYYY-MM-DD-TITLE.EXTENSION`{: .filepath} 中的 TITLE 不必与 `Front Matter` 中的 title 相同，建议采用小写英文加连字符的格式。

### 评论区

可以使用 Disqus、Utterances 或 Giscus 提供的评论系统服务，在 `_config.yml_`{: .filepath} 文件中的 `comments` 板块进行配置。本文使用 Disqus。

1. 在 [官网](https://disqus.com) 注册 Publishers 账号，然后点击右上角 `Settings` 选择 `Add Disqus To Site`，登记站点信息，获取（或设置）站点 Shortname，然后 `Create Site`。

2. 完成后点击 `Settings` 选择 `Admin`，选择刚刚的站点，点击 `Edit Settings`，在 `General` 中编辑 Website URL，在 `Ads` 中关闭广告，在 `Reactions` 中可以修改或关闭 Emojis。

3. 在 `_config.yml_`{: .filepath} 文件中的 `comments` 板块配置 `provider`（disqus）和 `shortname`。

### 访客统计

#### 参考资料

* [在github主页中增加访问量统计和google分析](https://qchaha.github.io/2018/09/19/homepageStatistics.html)
* [自部署busuanzi访问量统计服务](https://blog.liushen.fun/posts/e401be2d/)
* [如何自己搭建busuanzi不蒜子平台，搭建静态博客的访问量统计](https://blog.zhheo.com/p/b7bb7832.html)

#### 操作流程

本文使用 [Busuanzi](https://www.busuanzi.cc/) 显示访问次数，“两行代码，搞定计数”，在想要显示的地方插入代码即可。

1. 本站总访问量
``` html
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<span id="busuanzi_container_site_pv">本站总访问量 <span id="busuanzi_value_site_pv"></span> 次</span>
```

2. 本站总访客数
``` html
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<span id="busuanzi_container_site_uv">本站总访客数 <span id="busuanzi_value_site_uv"></span> 人</span>
```

3. 本页总阅读量
``` html
<script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
<span id="busuanzi_container_page_pv">本页总阅读量 <span id="busuanzi_value_page_pv"></span> 次</span>
```

#### 注意

1. 如果使用 127.0.0.1:4000 进行本地预览，由于域名不是我们的真实域名（`https://username.github.io`{: .filepath}），Busuanzi 给出的站点总访问量和总访客数不正确。

2. 很多静态博客都使用 Busuanzi 进行访客统计，因此其存在响应速度慢的问题。可以考虑自己搭建 Busuanzi 服务，参见上面的资料。

---

Everyone is welcome to reach out and exchange ideas.

Please credit the source: `Frogman's Blog (https://frogmanr9.github.io)`. Thank you.

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
