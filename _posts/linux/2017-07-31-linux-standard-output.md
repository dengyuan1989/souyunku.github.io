---
layout: post
title: Linux下 标准错误输出重定向
categories: Linux
description: Linux标准输出
keywords: Linux
---
 
## Linux下" >/dev/null 2>&1 "相关知识说明

> 在学习Linux的过程中,常会看到一些终端命令或者程序中有">/dev/null 2>&1 "出现,由于已经遇到了好几次了,为了理解清楚,百度了一下相关的知识。


``` sh
command >/dev/null 2>&1 &  == command 1>/dev/null 2>&1 &
```
 
- 0:表示键盘输入(stdin)
- 1:表示标准输出(stdout),系统默认是1 
- 2:表示错误输出(stderr)


- 1)command : 表示shell命令或者为一个可执行程序
- 2)> : 表示重定向到哪里 
- 3)/dev/null : 表示linux的空设备文件 
- 4)2 : 表示标准错误输出
- 5)&1 : &表示等同于的意思,2>&1,表示2的输出重定向等于于1
- 6)& : 表示后台执行,即这条指令执行在后台运行
 

## 1>/dev/null

- 表示标准输出重定向到空设备文件,也就是不输出任何信息到终端,不显示任何信息。

## 2>&1
- 表示标准错误输出重定向等同于标准输出,因为之前标准输出已经重定向到了空设备文件,所以标准错误输出也重定向到空设备文件。


> 这条命令的意思就是在后台执行这个程序,并将错误输出2重定向到标准输出1,然后将标准输出1全部放到/dev/null文件,也就是清空.
所以可以看出" >/dev/null 2>&1 "常用来避免shell命令或者程序等运行中有内容输出。



# Contact

 - 作者：鹏磊  
 - 出处：[http://www.ymq.io](http://www.ymq.io)  
 - Email：[admin@souyunku.com](admin@souyunku.com)
 - 版权归作者所有，转载请注明出处
 - Wechat：关注公众号，搜云库，专注于开发技术的研究与知识分享
 
![关注公众号-搜云库](http://www.ymq.io/images/souyunku.png "搜云库")