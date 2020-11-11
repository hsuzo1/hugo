---
title: "VS Code添加远程仓库"
date: 2020-10-22T09:42:40+08:00
draft: false
categories: ["tech"]
Tags : ["git"]
draft: False
---

接下来到当前Repository文件夹根目录中，如果没有初始化过，安装文章开始初始化的方法，进行初始化。 然后执行下面的命令
```bash
    git remote add origin https://github.com/xuanhun/vscode.git
    git pull origin master
 ```

现在我们查看一下.git文件夹下的config文件，可以看到添加了远程Reps地址。
再次修改MD，push