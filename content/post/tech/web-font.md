---
title: "网页中引用GoogleFont的方法"
date: 2020-10-29T19:18:06+08:00
draft: false
categories: ["tech"]
Tags : ["other", "googlefont"]
decription : 在线调用方式在Hugo网页中使用GoogleFonts的Noto Serif SC字体
---


>
>
>
>全世界是一个巨大的舞台，所有红尘男女均只是演员罢了。上场下场各有其时。每个人一生都扮演着许多角色，从出生到死亡有七种阶段。  
>———莎士比亚
	<!--more-->

	
一款优雅的字体能为网页增色不少，正如纤纤细手上的一枚戒指。
通过网页引用链接，在css中定义，是调用字体的便捷途径。
一般是**英文字体** &#8680; **苹果字体** &#8680; **中文字体**，最后再加**sans-serif**。
如下：
>*Mulish', -apple-system, 'Noto Serif SC', "PingFang SC", "Microsoft Yahei UI", "Microsoft Yahei", sans-serif;*

## 1.网络直接引用

```html
<!doctype html>
<html lang=zh-cn>
	<head>
        <link href="https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@300&display=swap" rel="stylesheet">
	</head>
	<body>
		<div>
			<p style="font-family: Inter,-apple-system,BlinkMacSystemFont,Roboto,'Noto Serif SC',Segoe UI,Helvetica,Arial,sans-serif; font-size:1rem">
            因为好奇心，想看看黑暗中有什么。
            12344567890
            ABCDEFGHIJKLMNOPQ
			</p>
		</div>
	</body>
</html>
```

## 2.本地化引用
1.在 **google-webfonts-helper**(https://google-webfonts-helper.herokuapp.com/fonts) 中选择想要的字体
{{< fancybox "/img/font" "Snipaste_2020-11-02_20-07-35.png" "search font" "font">}}  

2.左侧输入如**Noto Serif SC**，右侧勾**chinese-simplified** **latin**选**400、900**
{{< fancybox "/img/font" "Snipaste_2020-11-02_20-08-40.png" "select the font size" "font">}}
  

3.进行下载并解压到网站资源目录中  
{{< fancybox "/img/font" "Snipaste_2020-11-02_20-11-04.png" "download the zip package" "font">}}
  
4.新建一个css字体文件**googlefonts.css**，贴入下列内容  

{{< fancybox "/img/font" "Snipaste_2020-11-02_20-10-14.png" "paste the css script" "font">}}	
```css
/* noto-serif-sc-regular - latin_chinese-simplified */
@font-face {
  font-family: 'Noto Serif SC';
  font-style: normal;
  font-weight: 400;
  src: url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-regular.eot'); /* IE9 Compat Modes */
  src: local('Noto Serif SC'), local('NotoSerifSC-Regular'),
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-regular.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-regular.woff2') format('woff2'), /* Super Modern Browsers */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-regular.woff') format('woff'), /* Modern Browsers */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-regular.ttf') format('truetype'), /* Safari, Android, iOS */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-regular.svg#NotoSerifSC') format('svg'); /* Legacy iOS */
}
/* noto-serif-sc-900 - latin_chinese-simplified */
@font-face {
  font-family: 'Noto Serif SC';
  font-style: normal;
  font-weight: 900;
  src: url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-900.eot'); /* IE9 Compat Modes */
  src: local('Noto Serif SC Black'), local('NotoSerifSC-Black'),
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-900.eot?#iefix') format('embedded-opentype'), /* IE6-IE8 */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-900.woff2') format('woff2'), /* Super Modern Browsers */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-900.woff') format('woff'), /* Modern Browsers */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-900.ttf') format('truetype'), /* Safari, Android, iOS */
       url('../fonts/noto-serif-sc-v7-latin_chinese-simplified-900.svg#NotoSerifSC') format('svg'); /* Legacy iOS */
}
```  

5.在网页**head.html**中加入连接
```html
<link href="/css/googlefonts.css" rel="stylesheet">
```  

# &#128540;