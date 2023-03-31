---
title: "LightBox and HTML5 Video"
date: 2020-10-31T23:06:48+08:00
draft: false
categories: ["other"]
Tags : ["video", "4K", "lightbox"]
---



#### 1.以下内置单张图片显示
![imaged](/usegit/Vienna.Philharmon.jpg)

#### 2.HTML5方式播放4K视频
如果不启用confing.toml则无法使用
<div align="center">
    <video src="http://blog.hsuzo.cn:6001/Delphi.mp4" controls="controls" width="80%">
    您的浏览器不支持 video 标签。
    </video>
</div>


#### 3.内置播放youtube视频功能
{{\< youtube IdejM6wCkxA >}}
注意：需要外网连接才能正常播放
{{< youtube IdejM6wCkxA >}}  


#### 4..fancybox播放视频

{{\< fancyvideo src="path" >}} 
{{< fancyvideo src="https://vfx.mtime.cn/Video/2023/03/31/mp4/230331084255204126.mp4" >}}
  

测试短片 Asteroid City - Official Trailer ：
{{< fancyvideo src="https://hugo-1300132103.cos.ap-guangzhou.myqcloud.com/video/2023/Asteroid%20City%20-%20Official%20Trailer%20-%20Only%20In%20Theaters%20June%2016.mp4" >}}
  
   
#### 5.以下通过fancybox实现图片灯箱并滚动显示

{{< fancybox "/img" "esmeralda.jpg" "fancybox render" "gallery" >}}
</br>
{{< fancybox "/img" "notebook.jpg" "fancybox render" "gallery" >}}
</br>
{{< fancybox "/img" "Pope-Edouard-de-Beaumont-1844.jpg" "fancybox render" "gallery" >}}
</br>
{{< fancybox "/img" "Victor_Hugo-Hunchback.jpg" "fancybox render" "gallery" >}}
</br>