---
title: "SSH、SCP、SFTP使用"
date: 2020-11-10T10:32:02+08:00
draft: false
categories: ["tech"]
Tags : ["SSH", "SCP", "FTP"]
---


#### SSH背景
>笔者之前对于SSH(Secure Shell, 安全外壳协议)的了解仅仅知道一般用来进行访问远程主机、是用来替代telnet的不安全链接而发展的。常用的也只有ssh和scp，后来发现还有sftp，经过了解知道各个的作用。接下来讲叙述在学习和开发的过程中该如何使用他们。

## ssh
```bash
$ ssh
# out:
usage: ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface]
           [-b bind_address] [-c cipher_spec] [-D [bind_address:]port]
           [-E log_file] [-e escape_char] [-F configfile] [-I pkcs11]
           [-i identity_file] [-J [user@]host[:port]] [-L address]
           [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]
           [-Q query_option] [-R address] [-S ctl_path] [-W host:port]
           [-w local_tun[:remote_tun]] destination [command]
# eg：
$ ssh -p 50000 root@192.168.10.10
# 这个应该是用的最多的命令了，由于SSH的默认端口为22，
# 通常情况下对于没有独立ip的情况下，有时候会使用NAT技术会将22端口映射出来，这时候就需要加上-p指定端口
```

## scp
scp和cp命令相似，都是文件拷贝的命令，可以将本地文件拷贝到远程主机的目录下，也可以将远程主机文件拷贝到本地目录来。但是scp是安全的文件拷贝
```bash
$ scp
# out:
usage: scp [-346BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file]
           [-l limit] [-o ssh_option] [-P port] [-S program] source ... target
# eg:
$ scp -P 50000 /tmp/test.txt root@192.168.10.10:/tmp
# 将本地/tmp/test.txt上传到192.168.10.10这个ip地址对应的主机的/tmp目录下
```  

## sftp
这个命令我用的极少，经了解其实sftp和ftp相似，由于ftp是不安全的的协议，在Linux操作系统中ftp的实现软件叫做vsftp，而sftp是基于SSH协议基础上，带有安全的文件传输。他的用法也极为简单
```bash
$ sftp
# out:
usage: sftp [-46aCfpqrv] [-B buffer_size] [-b batchfile] [-c cipher]
          [-D sftp_server_path] [-F ssh_config] [-i identity_file] [-l limit]
          [-o ssh_option] [-P port] [-R num_requests] [-S program]
          [-s subsystem | sftp_server] destination

# eg:
$ sftp -P 50000 root@192.168.10.10
# out:
# sftp> 
-------------------------------------------------------------------------------------
sftp> help
# out:
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-afPpRr] remote [local]       Download file
reget [-fPpRr] remote [local]      Resume download file
reput [-fPpRr] [local] remote      Resume upload file
help                               Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing
lumask umask                       Set local umask to 'umask'
mkdir path                         Create remote directory
progress                           Toggle display of progress meter
put [-afPpRr] local [remote]       Upload file
pwd                                Display remote working directory
quit                               Quit sftp
rename oldpath newpath             Rename remote file
rm path                            Delete remote file
rmdir path                         Remove remote directory
symlink oldpath newpath            Symlink remote file
version                            Show SFTP version
!command                           Execute 'command' in local shell
!                                  Escape to local shell
?                                  Synonym for help
```
**注意**： ssh中的-p与 scp和sftp中的-P不一样，在实际操作的过程中可能出现错误。要多加注意。

### Q&A
>其实关于SSH的只是还有很多比如免密连接，使用ssh-keygen生成公钥和私钥，在以后连接之后就不需要在输入密码等。

> 文章转自
> [光明携带者的博客](http://eilene.cn/2019/04/07/SSH%E4%B8%89%E5%A4%A7%E5%88%A9%E5%99%A8-ssh%E3%80%81scp%E3%80%81sftp/)