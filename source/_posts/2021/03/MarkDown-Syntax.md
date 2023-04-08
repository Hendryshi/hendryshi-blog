---
title: MarkDown Syntax
cover: https://img.hendryshi.com/i/2023/04/6431c56e2494e.webp
categories:
  - Tech
tags:
  - MarkDown
  - Hexo
abbrlink: 7f1e6e2a
date: 2021-03-21 23:40:39
---


Records the mark down syntax

{% note info modern no-icon %}
There is a [**online tool**](http://www.atoolbox.net/Tool.php?Id=715) to transfer HTML code to Mark Down syntax
{% endnote %}

<!-- more -->

## Typography

### Add a picture

![PictureEx](https://img.hendryshi.com/i/2023/04/6431c5b958f9b.webp)

```
![](YouPictureName.png)
```
### HyperLink

[This is a link](https://www.google.fr/)

```
[Link Name](link url)
```

### Show code difference

```diff diff:layout/layout.jsx
<Head site={site} config={config} helper={helper} page={page} />
-   <body class={`is-${columnCount}-column`}>
+   <body class={`is-3-column`}>
<Navbar config={config} helper={helper} page={page} />
```

```
Use keyword "diff" after the code block, and use + or - to show difference

```diff diff:layout/layout.jsx
<Head site={site} config={config} helper={helper} page={page} />
-   <body class={`is-${columnCount}-column`}>
+   <body class={`is-3-column`}>
<Navbar config={config} helper={helper} page={page} />
```


### Reference

>Use the keyword ">" before your paragraph

### Mark text as a note/success/warning

[Note (Bootstrap Callout)](https://butterfly.js.org/posts/4aa8abbe/#Note-Bootstrap-Callout)

