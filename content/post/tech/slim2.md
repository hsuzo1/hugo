---
title: "如何使用Slimbox2显示图片灯箱"
date: 2020-11-13T09:03:21+08:00
draft: false
categories: ["tech"]
Tags : ["slimbox2","lightbox"]
---


本文主要介绍如何使用Slimbox 2。
Slimbox 2是一个JavaScript库，它在窗口中而不是在单独的窗口（弹出窗口）中将图像和照片显示为模式窗口。

[官方网站](http://www.digitalia.be/software/slimbox2)
>此库需要单独使用*jQuery*  

### 1.下载并安装
从[**这里**](http://www.digitalia.be/software/slimbox2#download)下载并解压缩。
解压缩后，将css和js目录放置在任何位置。
   
### 2.加载CSS和JS
将以下内容写在html文件的 **\<head>** 中，请根据您自己网站的环境更改路径。  
```html
<link href="css/slimbox2.css" type="text/css" rel="stylesheet" media="screen" />
<script src="js/jquery.js" type="text/javascript"></script>
<script src="js/slimbox2.js" type="text/javascript"></script>
<script type="text/javascript">
    $(document).ready(function() {
        $('a[rel*=lightbox]').slimbox();
    });
</script>
```  
  
### 3.使用方法
  
只需将 **rel =“ lightbox”** 属性添加到原始图片**a**元素链接中。
```html
<a href="images/sample1.jpg" rel="lightbox">画像1</a>
```
  
对于要分组并使其可导航的相关图像集，需要向属性**rel**的名称**lightbox**后添加一个组名，例如：  
```html
<a href="images/image-1.jpg" rel="lightbox-cats">image #1</a>
<a href="images/image-2.jpg" rel="lightbox-cats">image #2</a>
<a href="images/image-3.jpg" rel="lightbox-cats">image #3</a>
```


>如果在同一页面上有多个将灯箱应用到的链接，则当鼠标悬停在上方时，将始终显示“ PREV”和“ NEXT”按钮。  
  
### 4.显示图片标题
如果要在左下角显示图像的标题，请将**title**属性添加到a元素。  
如果需要，您甚至可以在标题中使用HTML。您必须用HTML实体替换 **<** 和 **>** 字符，并使用**单引号**而不是双引号。
```html
<a href="images/sample1.jpg" rel="lightbox" title="sample">画像1</a>
```  

### 5.其他功能选项
选项以以下形式设置：  
```javascript
$(document).ready(function() {
    $('a[rel*=lightbox]').slimbox({
        resizeDuration    : 200,
        imageFadeDuration : 200
    });
});
```  

| 选项                   | 初始值       | 说明                               |
| ------------------------ | --------------- | ------------------------------------ |
| loop                   | false    | 第一次或最后一次到达时是​​否存在循环 |
| overlayOpacity           | 0.8             | 叠加透明度                      |
| overlayFadeDuration      | 400             | 叠加速度                         |
| resizeDuration           | 400             | 加载图像时调整大小的速度 |
| resizeEasing             | swing          | 缓和                               |
| initialWidth             | 250             | 初始盒宽                         |
| initialHeight            | 250             | 初始盒子高度                   |
| imageFadeDuration        | 400             | 图像淡入速度                   |
| captionAnimationDuration | 400             | 字幕的出现速度                |
| counterText              | Image {x} sur {y} | 左下角显示的图像的索引文本 |
| closeKeys                | [27，88，67]  | “ CLOSE”键盘按键代码分配 |
| previousKeys             | [37，80]       | “ PREV”键盘的键代码分配  |
| nextKeys                 | [39，78]       | “ NEXT”键盘的键代码分配  |
