---
layout: post
title:  FlexLM lmremove 强制归还许可证操作方法
date: 2024-4-11
categories: blog
tags: [FlexLM,linux]
description: FlexLM lmremove 强制归还许可证操作方法。
---


根据 Flexlm user manaul 文档说明，我们可以看到 lmremove 命令有两种具体的用法：

强调：lmremove 既是一个单独命令，也是 lmtuil 命令的一个参数，执行 lmremove \*\*\* 和执行 lmutil lmremove \*\*\* 效果一致

            甚至有些 flexlm 的包里面，lmremove 仅仅是一个针对 lmutil 的 alias 而已。

    lmremove [-c license_file_list] feature user user_host display

或者

    lmremove [-c license_file_list] -h feature server_host port handle

在这两种用法中，建议使用第二种用法，因为参数更清晰更容易查找。

在使用之前，我们需要明确各个参数的含义。

\-c license\_file\_list     指定 license 文件。

feature                       feature 名称。

user                            用户名

user\_host                   用户调用 feature 的服务器名

display                        终端名（这个最难理解，也最不容易查找，因为这个选项问题，我们不建议使用第一种方法）

server\_host                License Server 名称

port                             License 运行的端口

handle                         程序运行的句柄

在明晰了这些参数的含义后，我们就需要了解如何获取需要的参数。这时我们需要用到 lmstat 命令，lmstat 命令的通常用法：

    lmstat -a -c port@hostname

通过 lmstat 命令，我们可以看到具体的 license 的使用情况

如：

    Users of Verdi:  (Total of 5 licenses issued;  Total of 1 license in use)

      "Verdi" v2021.09, vendor: snpslmd, expiry: 19-apr-2028
      vendor_string: ^1+S
      floating license

        user01 host05 2104470273015444378_1 (v2018.12) (server1/27000 787), start Wed 10/8 11:12

这里我只截取了部分显示结果。

解释一下相关参数信息（仅看最后一行）

user01                                         用户名 (对应 lmremove 中的 user)

host05                                         调用 feature 的服务器 (对应 lmremove 中的 user\_host)

2104470273015444378\_1        终端名 (对应 lmremove 中的 display，按照正常来讲，它应该显示类似于 /dev/tty\*\* 这种)

server1                                         license 服务器名 (对应 lmremove 中的 server\_host)

27000                                          license 端口号 (对应 lmremove 中的 port)

787                                               句柄号 (对应 lmemove 中的 handle)

 

鉴于终端名不易使用，因此我们建议使用 lmremove 的第二种方式。

使用方法：

    lmremove -c /license/xxxxx.lic -h Verdi server1 27000 787

注意：/license/xxxxx.lic 是 license 文件的绝对路径，相对路径亦可。

运行成功后并没有特别明显的提示

    lmutil - Copyright (c) 1989-2019 Flexera. All Rights Reserved.

如果运行失败，则会提示对应的参数不正确，我这里故意输入错误句柄号

    lmutil - Copyright (c) 1989-2019 Flexera. All Rights Reserved.lmremove: license handle 757 not found on this license server (server1)

再次执行 lmstat 命令，发现 license 已经被强制归还。

 
