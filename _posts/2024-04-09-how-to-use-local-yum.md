---
layout: post
title:  yum挂载本地源
date: 2024-4-09
categories: blog
tags: [yum,linux]
description: yum挂载本地源。
---

`mkdir   /xxx    创建一个目录
mount  /xxx/xxx.iso   /xxx   把镜像挂载到上一步创建的目录，必须是与主机版本相同的镜像`
设定永久挂载
`vim /etc/rc.d/rc.local    编辑开机自启动脚本的配置文件`
在该文件后面加入第3步的命令 mount  /xxx/xxx.iso   /xxx
修改权限，chmod +x /etc/rc.d/rc.local   使脚本有可执行的权限，以便开机自启动此脚本
设定系统安装源指向
mkdir /etc/yum.repos.d/backup   创建一个目录
`mv /etc/yum.repos.d/其他文件    /etc/yum.repos.d/backup   将yum.repos.d下的其他文件全部移动到目录下面，防止读取错误，或者直接删掉也可以,但是不要轻易删除文件，给自己留条后路。
vim /etc/yum.repos.d/yum.repo  创建一个.repo文件并编辑，内容如下： [haha]          这一行可以随便写
name=haha       name后面这个名字以可以随便写
baseurl=file:///XXX   XXX是刚才第3步镜像挂载的目录
gpgcheck=0
yum clean all   清除缓存
yum install httpd -y   安装http服务`
共享本地挂载：
1.在一台已经链接好本地yum源的主机中安装http服务\
`yum  install  httpd  -y     上面我已经做过了`
2.配置网络yum源
`systemctl start httpd                            开启http服务
systemctl stop firewalld                      关闭防火墙
systemctl  enable httpd                       使能http服务
systemctl disable firewalld                  关闭使能防火墙`
`mkdir   /var/www/html/rhel7   创建一个目录
mount /xxx/xxxx.iso  /var/www/html/rhel7    将镜像挂载到上一步所创建的目录中`
配置永久挂载：
`vim  /etc/rc.d/rc.local`\
`mount  /xxx/xxx.iso /var/www/html/rhel7
chmod  755  /etc/rc.d/rc.local`
重新配置指向性文件：
`vim /etc/yum.repos.d/yum.repo`  编辑yum.repo文件，内容如下：
`[rhel]          这一行可以随便写
name=rhel      name后面这个名字以可以随便写
baseurl=file:///XXX   XXX是刚才第3步镜像挂载的目录
gpgcheck=0`
在其他主机上使用共享网络yum源
1.配置其他主机的指向性文件
`vim  /etc/yum.repos.d/xxx.repo`

`yum  clean  all                             清除原有yum缓存
repolist                               列出仓库信息
install  software                安装软件
update                                  更新
list   software                        查看软件
list    all                                   查看所有软件
list    installed                        列出已安装软件
list    available                       列出可安装软件
reinstall    software                重新安装软件
remove    software                 卸载软件
info    software                        查看软件信息
search   software信息            根据软件信息查找软件
whatprovides  file                      根据文件找出包含此文件的软件
groups  list                                 列出软件组
groups  info                               查看软件组的信息
groups  install  sfgroup             安装软件组
groups  remove  sfgroup          卸载软件组`
