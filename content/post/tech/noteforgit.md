---
title: "GIT命令的常用方法"
date: 2020-10-25T21:34:23+08:00
draft: false
categories: ["tech"]
Tags : ["Hugo", "Git"]
Description: 记录一下GIT方法push网站的命令
---

### Hugo Package
```
wget https://github.com/gohugoio/hugo/releases/download/v0.76.5/hugo_0.76.5_Linux-64bit.tar.gz
tar -xzvf hugo_0.76.5_Linux-64bit.tar.gz
sudo mv hugo /usr/local/bin/hugo
hugo version
```
### 创建仓库hug0

##### 本地新建目录
g:\hugo\hugo\

新建一个测速网页文件index.html
```html
<html>
    <head>
        <title>我的第一个 HTML 页面</title>
    </head>

    <body>
        <p>body 元素的内容会显示在浏览器中。</p>
        <p>title 元素的内容会显示在浏览器的标题栏中。</p>
    </body>
</html>
```

```
git init
echo ^<h1^> Test Caddy Working^</h1^>
git add .
git commit -m "first Test"
git remote add origin https://github.com/hsuzo1/hug0
git push -u origin master
```

### push后在仓库的master下可见了

创建vps上的网页目录
```
mkdir /var/www
mkdir /var/www/hugo
cd /var/www
```

```
echo "deb [trusted=yes] https://apt.fury.io/caddy/ /" | sudo tee -a /etc/apt/sources.list.d/caddy-fury.list
sudo apt update
sudo apt install caddy
```

#### 修改caddy配置文件以通过域名访问
```
blog.hsuzo.cn
root * /var/www/hugo
file_server
```

再编辑Caddyfile
```
http://blog.hsuzo.cn
git github.com/hsuzo1/hug0 .
root /var/www/hugo

sudo setcap 'cap_net_bind_service=+ep' /usr/bin/caddy

curl https://getcaddy.com | bash -s personal http.git,http.hugo
```