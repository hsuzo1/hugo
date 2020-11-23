---
title: "在Hugo网页中动态引用JS与CSS文件"
date: 2020-11-09T20:08:25+08:00
draft: false
categories: ["tech"]
Tags : ["Hugo", "JS", "CSS"]
keywords: "Hugo JS CSS 动态调用"
description: 使用动态的方法在文章页面灵活调用js和css文件，从而减少主页面外部加载延时。
---



为了最大节约带宽和服务器资源，除了尝试在CDN中缓存CSS、js文件之外，我想到尽量设置网页不主动加载一些不必要的外部库。
例如，一些纯文字的页面就不需要jquery库的支持，而使用图片灯箱的页面则自动加载fancybox的资源库。    
因此，对于一些需要展示图片的网页，我就动态启用外部脚本链接。  
实现的方法也很简单，目前来看已经能满足我的要求，至于效果待以后再评估：  
#### 修改single.html
具体就是修改theme文件中的**themes\hello-friend\layouts\_default\single.html**，  
在倒数第二行插入如下代码：
```html
  {{ if (findRE "div class=.?fancybox.?" .Content 1)  }}
    <script src="https://cdn.jsdelivr.net/npm/jquery@3.5.1/dist/jquery.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/@fancyapps/fancybox@3.5.7/dist/jquery.fancybox.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/@fancyapps/fancybox@3.5.7/dist/jquery.fancybox.min.js"></script>
  {{ end }}
```  
  
*div class=.?fancybox.?* 为我的**shortcodes**中的代码：  
```html
    <div class="fancybox" align="center">
      <a data-fancybox="{{ $gallery }}" href="{{ $path }}/{{ $file }}" data-caption="{{ $caption }}"><img src="{{ $path }}/{{ $file }}"></a>
    </div>
```