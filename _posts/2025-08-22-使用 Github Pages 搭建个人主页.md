---
title: 使用 Github Pages 搭建个人主页
date: 2025-08-22 16:30:00 +0800
categories: [About]
tags: [guidelines, site]     # TAG names should always be lowercase

description: 本文介绍使用 Github Pages + Jekyll Chirpy 搭建个人主页的流程和注意事项。需注意的是，本文不涉及详细的个性化配置。
---

## 背景介绍

### 参考资料

* [【避坑篇】使用Github Pages搭建个人主页or博客网站【上】](https://zhuanlan.zhihu.com/p/641525444)
* [【快速部署+客制化】Github Pages+Jekyll Chirpy 速搭个人主页](https://zhuanlan.zhihu.com/p/695291923)
* [Chirpy notes](https://chirpy.cotes.page)
* [Jekyll docs](https://jekyllcn.com/docs/home)
* [GitHub Pages documentation](https://docs.github.com/en/pages)
* [Jekyll SEO tag](https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/usage.md)

### 环境

Windows 11 + VS Code

---

## 操作流程

### 安装 Ruby

1. 在 [官网](https://rubyinstaller.org/downloads) 下载最新版 WITH DEVKIT 的 Ruby。

2. 下载完毕打开 ridk install，在跳出的 CMD 中输入 3（选择“MSYS2 and MINGW development tool chain”）。

### 安装 Jekyll

1. 打开 CMD，安装 Jekyll
``` bash
gem install jekyll bundler
```

2. 查看是否成功安装
``` bash
jekyll -v
```

### 新建 Github 仓库

1. 有两种选择：
* 对于新手而言，建议使用 [chirpy-starter](https://github.com/cotes2020/chirpy-starter)，缺点是能做的改动较少；
* 如果对 Jekyll 比较熟悉或希望做较大改动，可以使用 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)。

这里介绍前一种。使用前一种时也可以将希望修改的文件从 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 中迁移过来进行改动。

2. 点击 `Use this templete`，`Create a new repository`，仓库命名为 `username.github.io`{: .filepath}。注意区分 Github 的 name 和 username，如果 username 中有大写则用小写。

3. 在新建仓库中点击 `Settings`，在 `Pages` 中将 Source 修改为 `Github Action`。

4. 可以将 `_config.yml`{: .filepath} 文件中的 url 改为自己的网址，此时从浏览器进入即可看到原始的网站了。

### 本地部署和调试

1. 打开 CMD，到目标目录，从 Github 上克隆刚刚创建的仓库到本地。

2. 打开 VS Code，到该仓库的根目录下，在终端输入
``` bash
bundle
```

3. 在 VS Code 终端输入
``` bash
bundle exec jekyll s
```
此时可以看到以下信息，从浏览器进入该网址即可实现本地预览。
> Server address: http://127.0.0.1:4000/  

### 网站基本配置

在 `_config.yml`{: .filepath} 文件中进行配置，包括但不限于：
* url
* avatar：侧边栏头像，可选择本地路径或链接。如选择本地路径，将图片放在 `assets/img/`{: .filepath} 中并在 `_config.yml`{: .filepath} 中填写路径
* title: 侧边栏名称
* timezone: 中国输入 Asia/Shanghai
* 注意 `social` 下的 `links` 至少要有一个链接，且注意格式（`- https://...`）

`_data/contact.yml`{: .filepath} 文件配置网站侧边栏底部诸 `contact` 按钮，对不想要的可以直接注释掉。

### 部署个人主页

把本地修改 commit 并提交到 Github 远程仓库上，可以触发 `Github Action` 的 `Build and deployment` 工作流进行部署。如果没有自动触发，可以手动点击 `Build and Deploy` 进行部署。

---

## 撰写文章

参见 [Writing a New Post](https://chirpy.cotes.page/posts/write-a-new-post)。
