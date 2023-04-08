---
title: Some modifications when updating Blog to Icarus version 4.0
cover: https://img.hendryshi.com/i/2023/04/6431b5ec67e6e.webp
categories:
  - Tech
tags:
  - Hexo
  - Blog
  - Icarus
abbrlink: e50f242f
date: 2021-03-21 20:47:50
---

> Update 2023/04/08: My blog has been moved to theme butterfly. This post may not be useful any more

The modification is based on the icarus version 4.x, version 2.0 of icarus uses the ejs.

The blog used the theme [**Icarus**](https://github.com/Hendryshi/hexo-theme-icarus) , and made some personal modifications. A lot of diff can be viewed directly in this blog. However, you can go to [**diff**](https://github.com/Hendryshi/hexo-theme-icarus/commit/6aed14fe15e8da8adf6565914f7a66db86fee9b7) if you want to review the modifications detailed

<!-- more -->

## Layout

### Article display by two-column

The theme defaults to a three-column layout, which seems a bit crowded when reading the article. You can turn all posts into a two-column layout through configuration, and display the required `widget` on the side in `_config.post.yml`, you can refer to [Official Document](https://blog.zhangruipeng.me/hexo-theme-icarus/Configuration/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/#%E5%B8%83%E5%B1%80%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)。

However, the overall width of the two columns is different from that of the three columns, so it is mandatory to specify a three-column layout and modify the corresponding width so that the width of all sidebars on the page remains the same.

```diff diff:layout/layout.jsx
<Head site={site} config={config} helper={helper} page={page} />  
-   <body class={\`is-${columnCount}-column\`}>  
+   <body class={\`is-3-column\`}>
 <Navbar config={config} helper={helper} page={page} />  
```

```diff diff:layout/layout.jsx
<Head site={site} config={config} helper={helper} page={page} />
-   <body class={`is-${columnCount}-column`}>
+   <body class={`is-3-column`}>
<Navbar config={config} helper={helper} page={page} />
```

```diff diff:layout/common/widgets.jsx
function getColumnSizeClass(columnCount) {
    switch (columnCount) {
        case 2:
-           return 'is-4-tablet is-4-desktop is-4-widescreen';
+           return 'is-4-tablet is-4-desktop is-3-widescreen';
         case 3:
            return 'is-4-tablet is-4-desktop is-3-widescreen';
    }
```

Optimize the width under different screen sizes

```diff diff:include/style/responsive.styl
 +widescreen()
+    .is-3-column .container
+        max-width: $widescreen - $gap
+        width: $widescreen - $gap
+
     .is-1-column .container, .is-2-column .container
         max-width: $desktop - 2 * $gap
         width: $desktop - 2 * $gap

 +fullhd()
+    .is-3-column .container
+        max-width: $fullhd - 2 * $gap
+        width: $fullhd - 2 * $gap
+
     .is-2-column .container
         max-width: $widescreen - 2 * $gap
         width: $widescreen - 2 * $gap
```

### Optimize article title layout

The title is moved to the top of the article information, the "Update time" is increased, and the icon is added

```diff diff:layout/common/article.jsx
                 {/* Metadata */}
                 <article class={`card-content article${'direction' in page ? ' ' + page.direction : ''}`} role="article">
+                    {/* Title */}
+                    <h1 className="title is-size-3 is-size-4-mobile has-text-weight-normal">
+                        {index ?
+                            <a className="has-link-black-ter" href={url_for(page.link || page.path)}>
+                                <i className="fas fa-angle-double-right"></i>{page.title}
+                            </a> :
+                            [<i className="fas fa-angle-double-right"></i>, page.title]
+                        }
+                    </h1>
                     {page.layout !== 'page' ? <div class="article-meta is-size-7 is-uppercase level is-mobile">
                         <div class="level-left">
                             {/* Creation Date */}
-                            {page.date && <span class="level-item" dangerouslySetInnerHTML={{
-                                __html: _p('article.created_at', `<time dateTime="${date_xml(page.date)}" title="${date_xml(page.date)}">${date(page.date)}</time>`)
-                            }}></span>}
+                            {page.date && <span class="level-item">
+                                <i className="far fa-calendar-alt">&nbsp;</i>
+                                <time dateTime="${date_xml(page.date)}" title="${date_xml(page.date)}">{date(page.date)}</time>
+                            </span>}
                             {/* Last Update Date */}
-                            {page.updated && <span class="level-item" dangerouslySetInnerHTML={{
-                                __html: _p('article.updated_at', `<time dateTime="${date_xml(page.updated)}" title="${date_xml(page.updated)}">${date(page.updated)}</time>`)
-                            }}></span>}
+                            {page.updated && <span class="level-item is-hidden-mobile">
+                                <i class="far fa-calendar-check">&nbsp;</i>
+                                <time dateTime="${date_xml(page.updated)}" title="${date_xml(page.updated)}">{date(page.updated)}</time>
+                            </span>}
                             {/* author */}
                             {page.author ? <span class="level-item"> {page.author} </span> : null}
```

Where the time directly uses the date

```diff diff:source/js/main.js
-    if (typeof moment === 'function') {
-        $('.article-meta time').each(function() {
-            $(this).text(moment($(this).attr('datetime')).fromNow());
-        });
-    }
```

### Optimize the layout at the end of the article

Add an `hr` at the end of the article and modify the display of `tags`. In the preview (home page), `tags` is also displayed, and the `Read More` button is placed on the right.

```diff diff:layout/common/article.jsx
                     {/* Licensing block */}
                     {!index && article && article.licenses && Object.keys(article.licenses)
                         ? <ArticleLicensing.Cacheable page={page} config={config} helper={helper} /> : null}
+                    <hr style="height:1px;margin:1rem 0"/>
+                    <div className="level is-mobile is-flex">
                     {/* Tags */}
-                    {!index && page.tags && page.tags.length ? <div class="article-tags is-size-7 mb-4">
-                        <span class="mr-2">#</span>
-                        {page.tags.map(tag => {
-                            return <a class="link-muted mr-2" rel="tag" href={url_for(tag.path)}>{tag.name}</a>;
+                    {page.tags && page.tags.length ? <div class="article-tags is-size-7 is-uppercase">
+                        <i class="fas fa-tags has-text-grey"></i>&nbsp;
+                        {page.tags.map((tag, index) => {
+                            return <a class="link-muted" rel="tag" href={url_for(tag.path)}>{tag.name}{index !== page.tags.length-1? ', ':''}</a>;
                         })}
                     </div> : null}
                     {/* "Read more" button */}
-                    {index && page.excerpt ? <a class="article-more button is-small is-size-7" href={`${url_for(page.link || page.path)}#more`}>{__('article.more')}</a> : null}
+                    {index && page.excerpt ? <a class="article-more button is-small is-size-7" href={`${url_for(page.link || page.path)}#more`}><i class="fas fa-book-reader has-text-grey"></i>&nbsp;&nbsp;{__('article.more')}</a> : null}
+                    </div>
                     {/* Share button */}
```

### Directory sticky positioning

Originally, only the overall sticky positioning of the sidebar was supported. For the reading experience, only the sticky positioning was turned on for the directory, the `column-left is-sticky` class was added, and the style was adjusted.

```diff diff:source/js/main.js
     if ($toc.length > 0) {
+        $toc.addClass('column-left is-sticky');
         const $mask = $('<div>');
```

```diff diff:include/style/widget.styl
+#toc
+    max-height: calc(100vh - 22px)
+    overflow-y: scroll
```


## Features

### Add feature license agreement

The new version already supports the license agreement, just configure it directly, refer to [Official Document](https://blog.zhangruipeng.me/hexo-theme-icarus/Configuration/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-%E4%B8%BB%E9%A2%98%E9%85%8D%E7%BD%AE/#%E6%96%87%E7%AB%A0%E8%AE%B8%E5%8F%AF%E5%8D%8F%E8%AE%AE)。

### Add feature title automatic count

```diff diff:include/style/article.styl
+.article {counter-reset:section}
+.article h2{counter-reset:sub-section}
+.article h3{counter-reset:composite}
+.article h4{counter-reset:detail}
+.article h2:before{content:counter(section) " ";counter-increment:section}
+.article h3:before{content:counter(section) "." counter(sub-section) " ";counter-increment:sub-section}
+.article h4:before{content:counter(section) "." counter(sub-section) "." counter(composite) " ";counter-increment:composite}
```


### Show directory by default

The new version supports direct configuration, just add `toc: true` in `_config.yml`.

## Style

### Modify logo and favicon

[Design Logo with Python](https://www.alphalxy.com/2019/03/python-logo/), and  Fine-tune the style.

### Button background color increased gradient

```diff diff:include/style/widget.styl
 .widget
     .menu-list
         li
             ul
                 margin-right: 0
+        a
+            transition: background-color 0.3s ease-in-out
         .level
             margin-bottom: 0
```

### card Increase floating effect

Increase the shadow when `:hover`, and increase the animation attribute `ease-in-out`.

```diff diff:include/style/card.styl
 .card
     overflow: visible
     border-radius: $card-radius
+    &:hover
+        box-shadow: 0 6px 15px rgba(0,0,0,0.15), 0 0 1px rgba(0,0,0,0.1)
```

```diff diff:source/js/animation.js
     setTimeout(() => {
         $('body > .navbar, body > .section, body > .footer').forEach(element => {
             element.style.opacity = '1';
-            element.style.transition = 'opacity 0.3s ease-out, transform 0.3s ease-out';
+            element.style.transition = 'opacity 0.3s ease-out, transform 0.3s ease-out, box-shadow 0.3s ease-in-out';
         });
```

```diff diff:source/js/animation.js
                     element.style.transform = '';
-                    element.style.transition = 'opacity 0.3s ease-out, transform 0.3s ease-out';
+                    element.style.transition = 'opacity 0.3s ease-out, transform 0.3s ease-out, box-shadow 0.3s ease-in-out';
                 }, i * 100);
```

### Modify the color of the tag

```diff diff:include/style/widget.styl
     .tags
         .tag:first-child
-            background: $primary
-            color: $primary-invert
+            background: whitesmoke
+            color: #4a4a4a

         .tag:last-child
-            background: $light-grey
+            background: #e7e7e7
             color: $white-invert
```

### Add profile rotation effect

```diff diff:include/style/article.styl
// Profile rotation
+.level img {
+    border-radius: 100px;
+    transition: all 0.3s ease-in;
+    -webkit-transition: all 0.3s ease-in;
+    -moz-transition: all 0.5s ease-in;
+    -o-transition: all 0.5s ease-in;
+}

+.level figure img:hover {
+    transform: rotate(360deg);
+    -webkit-transform: rotate(360deg);
+    -moz-transform: rotate(360deg);
+    -o-transform: rotate(360deg);
+    -ms-transform: rotate(360deg);
+} 
```