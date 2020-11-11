---
title: "MarkDownTips"
date: 2020-10-31T16:20:56+08:00
author: "david"
categories: ["tech"]
tags: ["markdown"]
---

使用内嵌HTML语言，需要在config.toml中加入如下：
```
[markup.goldmark.renderer]
  unsafe=true
```

## 参考式
参考式超链接一般用在学术论文上面，或者另一种情况，如果某一个链接在文章中多处使用，那么使用引用 的方式创建链接将非常好，它可以让你对链接进行统一的管理。
语法说明： 参考式链接分为两部分，文中的写法 [链接文字][链接标记]，在文本的任意位置添加[链接标记]:链接地址 “链接标题”，链接地址与链接标题前有一个空格。
如果链接文字本身可以做为链接标记，你也可以写成[链接文字][] [链接文字]：链接地址的形式，见代码的最后一行。
```bash
我经常去的几个网站[Google][1],[Demi的随笔和技术空间][2],[Demi的随笔和技术空间][]。

[1]:http://www.google.com
[2]:https://yuhongjun.github.io/ "Demi的随笔和技术空间"
[Demi的随笔和技术空间]:https://yuhongjun.github.io/
```
显示效果如下：
我经常去的几个网站[Google][1],[Demi的随笔和技术空间][2],[Demi的随笔和技术空间][]。

[1]:http://www.google.com
[2]:https://yuhongjun.github.io/ "Demi的随笔和技术空间"
[Demi的随笔和技术空间]:https://yuhongjun.github.io/

## 锚点
网页中，锚点其实就是页内超链接，也就是链接本文档内部的某些元素，实现当前页面中的跳转。比如我这里写下一个锚点，点击回到目录，就能跳转到目录。 在目录中点击这一节，就能跳过来。还有下一节的注脚。这些根本上都是用锚点来实现的。

注意：
Markdown Extra 只支持在**标题**后插入锚点，其它地方无效。
Leanote 编辑器右侧显示效果区域暂时不支持锚点跳转，所以点来点去发现没有跳转不必惊慌，但是你发布成笔记或博文后是支持跳转的。

```
## 0. 跳转测试{#index}
....................
。。。。。。。
...........
跳转到[跳转测试](#index)
```

## 注脚
语法说明：
在需要添加注脚的文字后加上脚注名字[^注脚名字],称为加注。 然后在文本的任意位置(一般在最后)添加脚注，脚注前必须有对应的脚注名字。
注意：经测试注脚与注脚之间必须空一行，不然会失效。成功后会发现，即使你没有把注脚写在文末，经Markdown转换后，也会自动归类到文章的最后。

```
使用 Markdown[^1]可以效率的书写文档, 直接转换成 HTML[^2], 你可以使用 Leanote[^Le] 编辑器进行书写。

[^1]:Markdown是一种纯文本标记语言

[^2]:HyperText Markup Language 超文本标记语言

[^Le]:开源笔记平台，支持Markdown和笔记直接发为博文
```
效果如下：
使用 Markdown[^1]可以效率的书写文档, 直接转换成 HTML[^2], 你可以使用 Leanote[^Le] 编辑器进行书写。

[^1]:Markdown是一种纯文本标记语言

[^2]:HyperText Markup Language 超文本标记语言

[^Le]:开源笔记平台，支持Markdown和笔记直接发为博文



## 粘贴 HTML 原始码
在代码区块里面， & 、 < 和 > 会自动转成 HTML 实体，这样的方式让你非常容易使用 Markdown 插入范例用的 HTML 原始码，只需要复制贴上，剩下的 Markdown 都会帮你处理，例如：
```html
<div class="footer">
   © 2016 ***
</div>
```
显示结果如下：
<div class="footer">
   © 2016 ***
</div>
---
## Markdown 嵌入HTML语法格式
```
这是<mark>高亮文本</mark>
```
这是<mark>高亮文本</mark>
```
<center>居中</center> 

** <center>居中加粗</center> **
```
<center>居中</center> 

**<center> 居中加粗</center>**

```
没有空格  
&ensp;半角空格  
&ensp;&ensp;半角空格两个  
&emsp;全角空格  
&emsp;&emsp;全角空格两个，以后就是用这个做为缩进  
```
没有空格  
&ensp;半角空格  
&ensp;&ensp;半角空格两个  
&emsp;全角空格  
&emsp;&emsp;全角空格两个，以后就是用这个做为缩进 

`<font face="华文行楷">华文行楷</font> ` 

<font face="华文行楷">华文行楷</font>

`<center><font face="华文行楷" size=100px color="red">华文行楷居中</font></center> `

<center><font face="华文行楷" size=100px color="red">华文行楷居中</font></center>  

```html
<font Size=1>我是几号字来着1</font>  
<font Size=2>我是几号字来着 --> 2号</font>  
<font Size=3>我是几号字来着 --> 3号</font>  
<font Size=4>我是几号字来着 --> 4号</font>  
<font Size=5>我是几号字来着 --> 5号</font>  
<font Size=6>我是几号字来着 --> 6号</font>  
<font Size=7>我是几号字来着 --> 7号</font>  

<center><font Size=5>居中加入5号字</font></center>  
<center><font size=5 face="华文行楷">华文行楷居中5号字</font></center>
```

<font Size=1>我是几号字来着1</font>

<font Size=2>我是几号字来着 --> 2号</font>

<font Size=3>我是几号字来着 --> 3号</font> 

<font Size=4>我是几号字来着 --> 4号</font>

<font Size=5>我是几号字来着 --> 5号</font>

<font Size=6>我是几号字来着 --> 6号</font>

<font Size=7>我是几号字来着 --> 7号</font>

<center><font Size=5>居中加入5号字</font></center>  

<center><font size=5 face="华文行楷">华文行楷居中5号字</font></center>
