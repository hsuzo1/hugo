---
title: "使用Nginx和Hugo建立静态网站"
date: 2020-11-11T15:28:15+08:00
draft: false
categories: ["tech"]
Tags : ["Hugo", "Nginx"]
cover : /usegit/web-developer.jpg
---

> *[pvere.net](https://pvera.net/posts/create-site-nginx-hugo/)*

> 请先准备域名并解析IP
  

### 安装服务器和设置防火墙
```bash
$ sudo apt install nginx ufw
$ sudo ufw allow 22
$ sudo ufw reload

$ sudo ufw allow 'Nginx Full'
$ sudo ufw reload
```
查看防火墙状态
```bash
$ sudo ufw status
```


### 配置 Nginx server
```bash
$ sudo systemctl start nginx
```
默认网页根目录在 **/var/www/**. 
创建新站点文件 **/etc/nginx/sites-available**  使用 **example.com**:  
```bash
$ sudo emacs /etc/nginx/sites-available/example.com
```
粘贴以下内容：
```bash
server {
    listen 80; # Configure the server for HTTP on port 80
    server_name example.com; # Your domain name
    root /var/www; # The files for your server
    index index.html; # You will create this file shortly
}
```
修改文件夹权限
```bash
$ sudo chmod 0755 /your/path/to/www
```

新建 **index.html** 测试Nginx是否正常运行:
```bash
$ sudo emacs /var/www/index.html
$ sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
$ sudo systemctl restart nginx
```

### 设置 SSL (HTTPS)

```bash
$ sudo apt install python-certbot-nginx
$ sudo certbot --nginx
$ sudo systemctl restart nginx
```
#### 设置自动更新证书
```bash
$ sudo crontab -e
```
设置为每天 09:00 运行，在最后一行添加如下：  
```  
0 9 * * * certbot renew --post-hook "systemctl reload nginx"
```
更新的时间可以灵活设置。First column specifies the minutes and the second specifies the hour (24h clock).

>Note: 由于使用SSL, 开启 gzip 可能会影响安全. 此处跳过。

### 其他功能配置

#### 启用 HTTPS/2
```bash
$ sudo emacs /etc/nginx/sites-available/example.com
```
将
``` 
listen 443 ssl; # managed by Certbot
```
替换成
```
listen 443 ssl http2; # managed by Certbot
```
重启Nginx server。


#### 启用客户端缓存

```bash
$ sudo emacs /etc/nginx/sites-available/example.com
```
将下方代码插入第一个域名解析块末尾：
```
server{
    # ...
    # Stuff
    # ...

    # Media
    location ~* \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|mp4|ogg|ogv|webm|htc)$ {
        expires 30d;
    }

    # CSS and Js
    location ~* \.(css|js)$ {
        expires 7d;
    }
```
  
  

### 设置 Hugo 站点

安装Hugo软件
```bash
$ sudo apt install hugo
```
创建新站点  
`$ hugo newsite sitename`

下载主题包  
`$ git clone https://github.com/Track3/hermit.git themes/hermit`

修改站点根目录下的配置文件**config.toml**，以启用新主题  
`theme = 'hermit'`

建立新文章  
`$ hugo new posts/some-post.md`

测试站点运行，在站点根目录下运行如下  
`$ hugo server -D`  
使用 **-D** 选项以显示草稿文章。 

编译生成站点文件
`$ hugo`
将会在站点根目录下新建一个**public**文件夹，里面包括网站的发布到外部服务器的所有内容。


如果web服务器是远程服务器，可以使用**rsync**软件将网页内容同步过去：
```bash
$ rsync -aAXv /path/to/sitename/public/ user@example.com:/var/www/ --delete
```
{{< notice tip >}}
 上面的命令需要确保**user**用户对远程服务器的**www**文件夹有**写入**权限，并且**SSH**端口处于开启状态。
{{< /notice >}}


使用自定义的404页面需要在Nginx中设置
**/etc/nginx/sites-available/yoursite.com**:
```
error_page 404 /404.html;
```