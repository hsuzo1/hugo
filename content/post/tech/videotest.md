---
title: "LightBox and HTML5 Video"
date: 2020-10-31T23:06:48+08:00
draft: false
categories: ["other"]
Tags : ["video", "4K", "lightbox"]
---



### 1.以下内置单张图片显示

![imaged](/usegit/Vienna.Philharmon.jpg)

### 2.HTML5方式播放4K视频

如果不启用config.toml则无法使用。  

{{< fancyvideo src="https://sample-videos.com/video321/mp4/240/big_buck_bunny_240p_1mb.mp4" >}}  


### 3.内置播放youtube视频功能

{{< youtube IdejM6wCkxA >}}  
使用方法：{{\< youtube IdejM6wCkxA >}}  
注意：需要外网连接才能正常播放


### 4..fancybox播放视频

测试短片 Asteroid City - Official Trailer ：
{{< fancyvideo src="https://hugo-1300132103.cos.ap-guangzhou.myqcloud.com/video/2023/Asteroid%20City%20-%20Official%20Trailer%20-%20Only%20In%20Theaters%20June%2016.mp4" >}}
使用方法：{{\< fancyvideo src="path" >}}   
   
### 5.以下通过fancybox实现图片灯箱并滚动显示

{{< fancybox "/img" "esmeralda.jpg" "esmeralda" "gallery" >}}
</br>
{{< fancybox "/img" "free-colorful-lights-wallpaper-1.jpg" "colorful lights" "gallery" >}}
</br>
{{< fancybox "/img" "Pope-Edouard-de-Beaumont-1844.jpg" "Pope Edouard de Beaumont-1844" "gallery" >}}
</br>
{{< fancybox "/img" "Victor_Hugo-Hunchback.jpg" "Victor Hugo Hunchback" "gallery" >}}
</br>