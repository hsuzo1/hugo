---
title: "LightBox and HTML5 Video"
date: 2020-10-31T23:06:48+08:00
draft: false
categories: ["other"]
Tags : ["image"]
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
{{< fancyvideo src="http://vfx.mtime.cn/Video/2019/03/18/mp4/190318231014076505.mp4" >}}
  

禁毒宣传短片1
{{< fancyvideo src="https://vod-2class.justmonkey.cn/31e9d7c56d8c4479960073ee0983b495/a60b152c28ec435e87bc52a905282a8c-5456d705cfd07e668f702e78be66cb6f.mp4" >}}
  
禁毒宣传短片2
{{< fancyvideo src="https://vod-2class.justmonkey.cn/e454c68d333445bc9ff2874619aede9c/59d0b71d5a45488b81208963c21f61ac-5456d705cfd07e668f702e78be66cb6f.mp4" >}}
   
   
#### 5.以下通过fancybox实现图片灯箱并滚动显示

{{< fancybox "/img" "esmeralda.jpg" "fancybox render" "gallery" >}}
</br>
{{< fancybox "/img" "notebook.jpg" "fancybox render" "gallery" >}}
</br>
{{< fancybox "/img" "Pope-Edouard-de-Beaumont-1844.jpg" "fancybox render" "gallery" >}}
</br>
{{< fancybox "/img" "Victor_Hugo-Hunchback.jpg" "fancybox render" "gallery" >}}
</br>