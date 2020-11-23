---
title: "Pure CSS Lightbox in Hugo theme"
date: 2020-10-27T16:20:56+08:00
draft: false
categories: ["tech"]
Tags : ["hugo", "CSS", "lightbox"]
cover : "https://hugo-1300132103.cos.ap-guangzhou.myqcloud.com/img/cover.png"
description: 使用pure css显示图片灯箱的方法
---

>from [jlelse's Blog](https://jlelse.blog/dev/css-lightbox-hugo/)

Hugo themes have the option to override the default rendering of Markdown images by using “Markdown Render Hooks” since version 0.62.0. By creating a file in **layouts/_default/_markup** named **render-image.html** it is possible to use a custom template for rendered images. This gives a lot of flexibility. I just used that flexibility to create a lightbox with pure CSS (without using JavaScript, like many lightbox implementations do). After clicking (or tapping) on an content image, you will see it filling the browser view. Another click brings you back to the content.

I already used the **render-image.html** file to display the alternative text as a caption below the image, but now I added the lightbox. My current image template looks like this:
{{< code language="html" title="render-image.html" id="1" >}}
<figure>
    <a href="#{{ .Destination | md5 }}-lightbox"><img src="{{ .Destination | safeURL }}" alt="{{ .PlainText }}"></a>
    <figcaption>
        {{ if .Title }}
        <p>{{ .Title | markdownify }}</p>
        {{ else if .Text }}
        <p>{{ .Text | markdownify }}</p>
        {{ end }}
    </figcaption>
</figure>
<a href="#_" class="lightbox" id="{{ .Destination | md5 }}-lightbox">
    <img src="{{ .Destination | safeURL }}" alt="{{ .PlainText }}">
</a>
{{< /code >}}

The **img** element inside the **figure** element is wrapped by an anchor element linking to another hidden (and wrapped) **img** by it’s id. The id is generated using the md5 hash of the image source location.

The stylesheet (in SCSS) for the lightbox looks like this:
```scss
.lightbox {
	display: none;
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	z-index: 999;
	width: 100vw;
	height: 100vh;
	background: rgba(0,0,0,0.8);
	img {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		max-width: 100%;
		max-height: 100%;
	}
	&:target {
		outline: none;
		display: block;
	}
}
```
The lightbox has a fixed position and covers the whole viewport by setting width and height to 100% of the viewport (**100vw** and **100vh**). Inside that container the image is centered by using an absolute positition, setting the **top, left, right and bottom** positions to **zero** and setting **automatic** margin spacing.