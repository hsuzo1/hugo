---
title: "使用Cloudflare的Pages托管Github的Hugo静态页面"
date: 2023-03-31T21:59:44+08:00
draft: false
categories: ["tech"]
Tags : ["Hugo"]
description : 使用Cloudflare的Pages托管网页
keywords : "Hugo Cloudflare Github "
---


CloudFlare Pages 向免费用户提供了无限的站点数、请求数和带宽，除此之外付费用户可以获得更多的构建次数和同时构建站点数。声称Pages are super fast, always up to date, and deployed directly from your Git provider.
{{< image src="https://hugo-1300132103.cos.ap-guangzhou.myqcloud.com/img/2023/cloudflare-free.jpg" alt="cloudflarepages" position="center" style="border-radius: 8px;" >}}

## 1.创建项目
两种方式：连接Git账户或者直接上传文件，并设置仓库。
{{< image src="https://hugo-1300132103.cos.ap-guangzhou.myqcloud.com/img/2023/2023-03-31_221725.png" alt="cloudflarerepodory" position="center" style="border-radius: 8px;" >}}


## 2.设置变量并部署
在环境变量设置中添加一个变量：  
**HUGO_VERSION，值为：0.92.0。** 当然，版本号可以根据自己的情况设置。
