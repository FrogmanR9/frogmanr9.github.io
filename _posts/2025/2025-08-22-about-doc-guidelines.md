---
title: "关于本站与文档规范"
date: 2025-08-22 14:30:00 +0800
categories: [Web]
tags: [guidelines, style]     # TAG names should always be lowercase

description: 本文介绍建站目的和中英文混排文档规范。
---

## 关于本站

本站建于 2025 年 8 月 20 日，基于 Jekyll 构建，采用 Chirpy 主题，并部署在 GitHub Pages 上。

本站地址：[https://frogmanr9.github.io](https://frogmanr9.github.io)。

从小学时起就想建一个个人博客，然而一方面向来是雷声大雨点小，始终没有动手，另一方面感到自己既无文采，也无技术，没什么可写，故而迟迟未能成行。而今终究是搭了一个，希望不要三分钟热度。

本站用作个人博客，写一些专业和兴趣相关的文章，一来聊以自娱，二来也希望为社区做一点微小的贡献。

欢迎大家通过评论或邮件与我交流。

---

## 文档规范

当前主要进行中文写作，涉及专业相关，难免遇到中英文混排的问题，主要是标点和空格的问题。

作者参考了以下资料，综合自己的理解，制定本节规范，用于个人写作：
* [夹用英文的中文文本的标点符号用法（草案）](https://www.moe.gov.cn/jyb_sjzl/ziliao/A19/201001/t20100115_75604.html)
* [中文技术文档的写作规范](https://github.com/ruanyf/document-style-guide)
* [简体中文文本排版指南](https://zhuanlan.zhihu.com/p/49729668)

基础的中英文和 Markdown 语法及标点使用规范不在此赘述。

使用 Jekyll 构建博客时的写作注意事项参见 [撰写文章](https://frogmanr9.github.io/posts/githubpages-jekyll-chirpy/#撰写文章)。

### 标点符号

1. 在中文语段中，按中文规范，使用中文标点；纯英文语段中，按英文规范，使用英文标点。
> 例1：FAANG 是美国市场上最受欢迎和表现最佳的五大科技股的首字母缩写，即 Facebook（FB）、Apple（AAPL）、Amazon（AMZN）、Netflix（NFLX）和 Alphabet（GOOG）。  
> 例2：乔布斯在斯坦福大学毕业典礼上的演讲让人印象深刻，其中名句“Stay hungry, stay foolish”更是被很多人奉为座右铭。

2. 表示时间时，应使用半角冒号，前后不加空格。
> 例：现在是早上 8:00。

3. 十进制数字中用作分隔符的逗号使用半角符号，前后不加空格。
> 例：\$12,000,000.00

4. 以下场合使用直线连接号（`-`），占一个半角字符的位置：
* 两个名词的复合
> 例：氧化-还原反应
* 图表编号
> 例：图 2-1

5. 数值范围（例如日期、时间或数字）应该使用波浪连接号（`～`），占一个全角字符的位置。注意，波浪连接号前后两个值都应加上单位。
> 例：2000 年～2021 年

6. 波浪连接号也可用“至”代替。
> 例：本日气温为 -4°C 至 10°C。

### 空格

1. 数字使用半角字符，在空格规则中视同半角英文字符。

2. 全角中文字符与半角英文字符和阿拉伯数字之间应有一个半角空格。
> 例：1643 年 1 月 4 日，牛顿（Isaac Newton）诞生了。

3. 数字和英文单位之间保留空格。
> 例：这款手机存储容量是 256 GB。

4. 半角英文字符和阿拉伯数字与中文标点之间不加空格。

5. 段落内的内联代码、链接与字符、数字之间加半角空格，与标点之间不加空格。
> 例：`pwd` 命令可显示当前工作目录的绝对路径。

6. 加粗、删除线等文本样式与字符、数字之间加半角空格，与标点之间不加空格。

### Markdown 语法

1. 内联代码 `Inline Code` 在本站文章中的使用：
* 代码、命令
* 文件路径
``` markdown
`/path/to/the/file.extend`{: .filepath}
```
* 变量名、类名、属性名等
``` markdown
请确保每个 badge 都有 `alt` 属性，否则 HTML-Proofer 会报错。
```
* 网站 tab
``` markdown
点击 `Settings` 可以看到相关选项。
```
* 希望可以被读者直接选中复制的文本
``` markdown
Please credit the source as `Frogman's Blog (https://frogmanr9.github.io)` and include the link to this article (`{{ page.url | absolute_url }}`). Thank you.
```

2. 代码块需注明语言，必要时注明具体文件（及路径）。

3. 在本站文章中使用二至五级标题。标题下方空一行。

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
