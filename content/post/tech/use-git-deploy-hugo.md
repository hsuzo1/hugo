---
title: "用Hugo创建Blog站点并部署到VPS"
date: 2020-10-26T21:01:00+08:00
draft: False
categories: ["tech"]
author : "David"
Tags : ["Hugo", "Github", "vps"]
description: 用Hugo创建Blog站点，并自动化部署到VPS上。

---



>本文来源于[XEON CHOW](https://enit.xyz/tech/deploy-hugo-on-vps/)博客，并进行修改。

{{< image src="/usegit/Snipaste_2020-10-26_21-34-06.png" alt="Hello Friend" position="center" style="border-radius: 8px;" >}}


### 基本思路
>Blog 网站整个的搭建流程大致如下：
Windows 本地用 Hugo 进行网站的配置和编辑调试；
使用 Git 工具将 Windows 本地生成的网站文件夹远程部署到 VPS 服务器上；
VPS上通过Git Hook自动编译发布网页文件
VPS 上使用 Nginx 作为 Web 网页服务器。
网页申请 SSL 证书并强制开启 HTTPS

## 一、VPS端配置情况

### 1.安装Hugo客户端以及rsync
方式一
```shell
sudo apt-get install hugo
hugo version
```

方式二
```shell
wget https://github.com/gohugoio/hugo/releases/download/v0.76.5/hugo_0.76.5_Linux-64bit.tar.gz
tar -xzvf hugo_0.76.5_Linux-64bit.tar.gz
sudo mv hugo /usr/local/bin/hugo  # 随便您想放置的目录即可
hugo version

apt install rsync
```

### 2.安装Git仓库，并配置权限
```shell
apt-get update
apt-get install git-core
```

添加 git 用户
```shell
useradd git
vi /etc/sudoers
```

#### 1.在  User privilege specification 配置字段下添加
`git	ALL=(ALL:ALL) ALL`
从而使得 git 用户拥有 sudo 权限

#### 2.给 sudoer 文件添加权限
`chmod 740 /etc/sudoers`

#### 3.编辑 /etc/passwd 文件
`vi /etc/passwd`
将最后一行 git 用户的 /bin/sh 改为 /bin/bash，是 git 用户也拥有 bash 脚本解释器。

#### 4.创建 git 用户密码，并建立用户目录、配置权限
```shell
passwd git
mkdir /home/git
chown -R git:git /home/git
```



## 新建 VPS 端的 git 仓库

#### 1.在 VPS 上新建一个 git 仓库作为远程仓库。切换到 git 用户并转到 git 用户文件夹
```shell
su git
cd /home/git
mkdir blog.git
cd blog.git
```


#### 2.初始化仓库
`git init --bare`


#### 3.在 /home/git 路径下创建存放 SSH 公钥的文件夹 .ssh 并进入，在该文件夹下新建公钥文件并编辑
```shell
cd /home/git
mkdir .ssh
cd .ssh
vi authorized_keys
```
将前文 Windows 本地 /c/Users/Xeon/.ssh/ 位置生成的 SSH 公钥文件 id_rsa.pub 内容复制粘贴到 authorized_keys 文件中并 ：wq 保存。

#### 4.新建网页文件存放目录，通过root用户新建 /var/www 路径，并转到 /var/www 文件夹
```shell
exit
mkdir -p /var/www
cd /var/www
mkdir blog
```

#### 5.修改 /var/www/blog 文件夹权限，给用户Git赋予/tmp的操作权限，提示后输入root密码：
```shell
chown -R git:git /var/www/blog/
sudo chmod -R 777 /tmp
```


### 设置 git 钩子脚本

创建一个 git 钩子脚本，主要目的是当 Windows 本地的文件被远程推送到 VPS 的 blog.git 仓库时，触发脚本自动将 blog.git 里的内容。

#### 1.切换为 git 用户和文件夹，新建 post-receive 脚本文件
```shell
su git
cd
cd blog.git/hooks/
vi post-receive
```

post-receive内容如下：
```bash
#!/bin/bash -e

# A place to clone the remote repo so Hugo can build from it
WORKING_DIRECTORY=/home/git/clone
# Backup folder in case something goes wrong during this script
BACKUP_WWW=/home/git/backup_html
# Domain name so Hugo can generate links correctly
MY_DOMAIN=blog.chihuashi.xyz
GIT_REPO=/home/git/blog.git
#TMP_DIR_CLONE=/tmp/blog
PUBLIC_WWW=/var/www/blog

# If something goes wrong, put the previous version back in place
function cleanup {
    echo "A problem occurred. Reverting to backup."
    rsync -aqz --del $BACKUP_WWW/ $PUBLIC_WWW
    rm -rf $WORKING_DIRECTORY
}

trap cleanup EXIT

# Clear out the working directory
rm -rf $WORKING_DIRECTORY

# Make a backup copy of current website version
rsync -aqz $PUBLIC_WWW/ $BACKUP_WWW

# Clone the new version from GitHub
git clone $GIT_REPO $WORKING_DIRECTORY

# Delete old version
rm -rf $PUBLIC_WWW/*
# Have Hugo generate the new static HTML directly into the public WWW folder
/usr/bin/hugo -s $WORKING_DIRECTORY -d $PUBLIC_WWW -b "https://${MY_DOMAIN}"

# Clear out working directory
# cp -rf $TMP_DIR_CLONE/* $PUBLIC_WWW
rm -rf $WORKING_DIRECTORY
# Exit without trapping, since everything went well
trap - EXIT
```

2.给 post-receive 脚本文件添加可执行权限
```shell
chmod +x post-receive
```


## 二、Windows 本地 Git 设置

在 Windows 本地安装 Git 的方法参考其他。

安装后需要在本地新建一个 git 用户，设置用户名和邮箱（仅作用户识别使用，可填写任意邮箱地址）。在任意路径下打开 Git Bash 命令窗口，输入以下指令：
```
git config --global user.name "name"
git config --global user.email "emailaddress"
```
其中 "name" 和 "emailaddress" 字段填入自己的用户名和邮件地址（均可任意填写）。

为了后续远程推送文件方便，在本地生成 ssh 密钥备用：
`ssh-keygen -t rsa -C "emailaddress"`
bash 会要求输入 Windows 用户的密码，无密码直接 enter。从而在 c/Users/Xeon/.ssh/ 路径下会生产 ssh 密钥，有公钥 id_rsa.pub 和密钥 id_rsa 两个文件。

在 Hugo 生成的静态网页文件夹，即 D:\Hugo\Sites\blog\public 文件夹中打开 Git Bash 窗口，输入以下命令：
```
git init
git add --all
git commit -m "Description"
```
上面三个命令的意思分别是：
创建 git 仓库；
添加全部文件；
提交文件，其中 -m 表示本次更新的描述，描述内容为 "Description" 中的内容，按情况自行填写。
从而本地的 Git 仓库搭建完成，远程提交静态网页文件之前需要现在 VPS 上进行设置。


## 三、Windows 本地推送

首先在 Windows 本地测试一下能否以 git 用户 ssh 登陆到 VPS
`ssh -p "22" git@VPS_ip`
若提示 WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!，可将 Windows 本地 /c/Users/Xeon/.ssh/ 下的 known_hosts 文件删除。

本地推送

回到已经创建为本地仓库的 D:\Hugo\Sites\blog\public 文件夹中打开 Git Bash 窗口，输入以下命令添加远程仓库：
```
git remote add origin git@your_VPS_IP:/home/git/blog.git
git remote set-url origin ssh://git@your_VPS_IP:SSH_Port/home/git/blog.git
```
以上命令中的 your_VPS_IP 为你 VPS 的 IP，SSH_Port 为你的 VPS SSH 登陆端口（一般默认为22）。
以上命令仅在初次推送前设置，以后无需设置。

推送本地仓库到 VPS：
`git push origin master`

此时用浏览器访问域名 your_domain.com，即可打开自己的 blog 站点，并且自动开启了 HTTPS 加密。

以后 Windows 本地更新内容后，只需在 D:\Hugo\Sites\blog\public 路径下打开 Git Bash 输入以下命令：
```shell
git init
git add --all
git commit -m "description"
git push origin master
```
即可将更新内容推送到 VPS。

>自定义映射远程服务器IP地址的域名
每次远程登录都要这样ssh root@114.xxx.xxx.xxx，很麻烦有没有？！没有配域名的而且IP地址复杂后更加恶心有没有？！来一起配置一下对于本地的服务器的域名！编辑/c/windows/system32/drivers/etc目录下的hosts文件，用editplus打开即可，如下添加一行：
114.xxx.xxx.xxx (空格) ts.aliyun.com (回车换行)



## 四、有关证书申请和配置

使用 acme 申请 SSL 证书，在此之前先要做好域名的解析。我使用 CloudFlare 进行域名解析，因此可以直接使用 CloudFlare 的 API 进行申请，比较方便，若使用其他解析服务，需要手动添加两条 txt 解析，可自行搜索教程。以下步骤均在 root 用户下。

#### 1.安装 acme
新建一个文件夹 /home/tls 存放证书和密钥
```shell
rm -rf /home/tls && mkdir -p /home/tls
curl https://get.acme.sh | sh
source ~/.bashrc
```

#### 2.导入 CloudFlare 的 API
在 CloudFlare 的个人账户页面，找到 API Tokens-Global API Key，点击 view 查看，将 API Key 复制保存下来。在 VPS 上输入以下命令：
```shell
export CF_Key="Your_API_Key"
export CF_Email="Your_CloudFlare_Email"
```
其中 CF_Key 和 CF_Email 填入自己的 API Key 和 CloudFlare 账户邮箱。

#### 3.申请证书
`~/.acme.sh/acme.sh --issue --dns dns_cf -d your_domain.com -d *.your_domain.com -k ec-256`
以上命令中的 your_domain.com 替换为自己的域名，这个命令会生成 *.your_domain.com 泛域名的 SSL 证书，即 your_domain.com 下所有的二级、三级域名都会受到证书保护。所申请到的证书文件存放在 ~/.acme.sh/your_domain.com_ecc 文件夹下，~ 代表当前用户目录，若当前是 root 用户，则 ~ 代表 /root。

#### 4.安装证书并启用自动更新
将证书安装到 /home/tls 路径下
```
~/.acme.sh/acme.sh --installcert -d your_domain.com -d *.your_domain.com --fullchainpath /home/tls/your_domain.com.crt --keypath /home/tls/your_domain.com.key --ecc
acme.sh --upgrade --auto-upgrade
```
同样将以上命令中的 your_domain.com 替换为自己的域名，此时在 /home/tls 路径下会生成证书文件 your_domain.com.crt 和密钥文件 your_domain.com.key。第二行命令设置证书到期自动更新。

#### 5.VPS 上 Nginx 设置

安装 Nginx
```shell
apt-get install nginx -y
vi /etc/nginx/sites-enabled/default
```
用以下内容覆盖原配置default：
```json
server {
  # 当 http 协议被请求时，统一转发到 https 协议上
  listen 80;
  listen [::]:80; # IPV6 协议
  server_name your_domain.com;
  rewrite ^(.*)$ https://$host$1 permanent;
}

server {
  listen 443 ssl;
  listen [::]:443 ssl;
  ssl_certificate /home/tls/your_domain.com.crt; # 证书的绝对路径
  ssl_certificate_key /home/tls/your_domain.com.key; # 证书密钥的绝对路径

  server_name your_domain.com;

  location / {
    root /var/www/blog/; #站点文件的路径
    index index.html;
  }
  
  # 证书验证 location
  location /.well-known/acme-challenge/ {
    root /home/tls/; # 证书文件夹的路径
    log_not_found off;
  }
}
```
同样，将 your_domain.com 替换为自己的域名。以上 Nginx 配置文件的大概含义见注释，详细含义见 Nginx 官方文档。

##### 修改配置文件后需要重启 Nginx
```shell
systemctl restart nginx
```