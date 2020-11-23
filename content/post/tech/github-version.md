---
title: "Github版本控制的方法"
date: 2020-10-27T18:34:17+08:00
draft: false
categories: ["tech"]
Tags : ["github"]
keywords: "github git 版本控制"
description: Github版本控制的方法
---


>- [https://github.com](https://github.com)
>- 官网登录
>- 下载git客户端

#### 1.生成秘钥&#128220; 打开git客户端，执行命令
```shell
ssh-keygen -t rsa -C "github的邮箱账号"
```
注意，需要给.ssh的7000权限
```shell
sudo chmod -R 700 ~/.ssh
```
去这个目录，寻找**id_rsa.pub**文件，把文件的字符串复制。
登录官网github.com右上角人头像&#129333;&#10140;setting a ssh and gpg keys a new ssh key&#10140;制的内容&#10140;add ssh keys


#### 2. 验证秘钥

git客户端输入`ssh -T git@github.com` ,按提示输入 yes 提示successfully即可


#### 3. 在服务器添加完公钥之后，ssh服务器然后报了这个错误
```
sign_and_send_pubkey: signing failed: agent refused operation
```
然后执行了以下命令才好。。
```
eval "$(ssh-agent -s)"
ssh-add
 ```


#### 4.建立身份标识
```
git config --global user.name  'github邮箱用户’
git config --global user.email  github邮箱用户
```
 
 


---




### 案例1：把项目提交到github仓库

进入d盘，你的项目地址
>注意，有时候一些文件隐藏了.gitignore,凡是在目录中包含这个文件，将被忽略提交到github
```shell
cd /d
mkdir www
cd www
```
创建一个文件随便输入字符保存退出.设置全局编码，防止代码乱码
```
git config --global gui.encoding utf-8
```
把项目缓存本地仓库
```
git add *
```
提交到本地仓库并备注
```
git commit –m “修改了文件”
```
推送到github的仓库
>如何创建github仓库，登录官网，人头像旁有个+号 new repository
创建完毕有有一个项目地址https://xxx.com/unrelaxs/test.git
创建完仓库后，提交到github仓库

配置一下项目的地址
`git remote add origin https://xxx.com/unrelaxs/test.git`
把本地分支推送到服务器分支上–u表示如果仓库没有则新建一个
`git push –u origin master`[默认是master，如果有版本号则填写版本号]
 
接着刷新**https://xxx.com/unrelaxs/test.git**这个网址，就能到你修改的内容了
 


### 案例2：修改了文件，却没有任何的操作，想恢复到之前的代码


.代表所有文件 或者也可以指定具体路径下的文件
`git checkout -- .`


### 案例3： 修改了文件，commit到缓冲区了（经过了add 和 commit），但是没有提交到github
`git reset HEAD .`


### 案例4：删除了文件如何恢复？？
```bash
git log
```
会弹出一个修改的列表：如
```
commit c2d7f0dc6196c87e636d121bf7f163ad31d8915d
```

commit 后的就是id了
恢复54:35时间的东西
```bash
git checkout id .
``` 


### 案例5：如何把对应版本代码克隆到本地

```bash
git clone –b 版本号 代码链接
```


### 案例6：在修改代码前，必须要做的事


>在修改前，必须是git pull 更新本地代码
 

### 案例7：修改本地代码如何提交到服务器？？

```bash
git pull origin 1.0(指定1.0版本)或者 git pull
git add .
git commit –m “修改了test文件”
***git push 后的origin1.0 是指定版本，可不填写***
git push origin 1.0
```
 

### 案例8：从git上获取项目？？
```bash
git init 初始化一个仓库
git remote add origin 网址
git pull origin 分支
git checkout -b xx分支  #如果没有该分支则尝试会创建这个分支
```


### 案例9：从git上面 clone下来的项目后，如何拉取次分支以及合并主分支的代码呢？
```
git init
git clone http://xxxx
git branch -r 查看远程仓库的分支，如果没有，请重新操作，还是不行，只能百度了
git pull origin 分支名 注意了 origin是仓库名名，，如果 git branch -r 查看到 xx/origin/mobile  则第一个斜杠后，即origin为仓库名，mobile为分支名
```
 

### 案例10：从git上面 clone下来别人的项目后，如何提交到自己项目上？
```
git init
git clone http://xxx
git remote add origin http://你自己的项目地址
git pull origin master --allow-unrelated-histories
git add .
git commit -m "版本合并"
git push origin master
```


### 案例11：屏蔽某个目录，某个文件不提交到git

**.gitignore**文件语法如下:
```bash
# 此为注释 – 将被 Git 忽略

*.a       # 忽略所有 .a 结尾的文件

!lib.a    # 但 lib.a 除外

/TODO     # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO

build/    # 忽略 build/ 目录下的所有文件

doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```
在项目根目录创建 **.gitignore**


### 案例12：新建一个分支，拉取另外一个分支代码, 并把新的分支合并到另外一个分支
```bash
git checkout -b feature_line
git pull origin develop  //拉取develop代码
git add .
git commit -m "创建了个分支"
git push origin feature_line
git checkout -b develop   //切换develop分支
git pull origin develop
git merge feature_line   //合并分支
git push origin develop //推送合并后的结果
```  
<br>
<br>
<br>

### 案例13：同个电脑用不同的账号，账号对应不同的 github平台

1. 生成**sshkey ssh-keygen -t rsa -C "邮箱账号"**
注意不要全部回车键  这里要写存储的路径如
```shell
$ ssh-keygen -t rsa -C "gg"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa): 
```
这里填写绝对路径 或者相对路径 命名两个账号都不同如：**/C/Users/Administrator/.ssh/id_rsa_one**

接着全部回车键，默认
```shell
$ ssh-keygen -t rsa -C "gg"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa): 
```
这里填写绝对路径 或者相对路径 命名两个账号都不同如：**/c/Users/Administrator/.ssh/id_rsa_two**

2. 进去/c/Users/Administrator/.ssh/ 这个目录
把 id_rsa_one.pub 跟two.pub 的秘钥 分别粘贴到不同的平台

3. 在这个目录 /c/Users/Administrator/.ssh/ 创建一个config文件，注意没有后缀
config文件内容如下：
```
Host github.com
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_two

Host github_1033
  HostName gitee.com
  IdentityFile ~/.ssh/id_rsa_one
```
注意了，上面我分别是 github.com 分别对应的是 github.com的平台，秘钥是**id_rsa_two** ，在github后台别粘贴错秘钥！！

而 github_1033 对应是 gitee.com的平台（码云），秘钥是 **id_rsa_one**

 
4. 在添加源的时候
```
git remote add origin git@github_1033:unrelaxs/object-design.git
```
码云明天的ssh项目地址是：**git@gitee.com:unrelaxs/object-design.git**
因此我把gitee.com改为github_1033就行了

 

4.1对比两个commit，并导出所有的文件
```
git archive -o ./export.zip 39e4d8cc9744877999b3f0554b48afe01ae83114 $(git diff --diff-filter=ACMR --name-only 25a0a906f4eb11d1da9654048c3af95ac50a8dac 39e4d8cc9744877999b3f0554b48afe01ae83114)
```
a0ca27310aab94205d18c3415bd40b9821de2650 开始 commitId
7b9cead0f411ce42637f7633cfbb7c9078d3202e 结束 commitId
 

5. 忽略某文件夹下所有的内容提交，但保存该文件夹
在文件夹下 创建一个.gitignore，内容如下
```
# ignore all except .gitignore file
*
!.gitignore
```
&#128540;