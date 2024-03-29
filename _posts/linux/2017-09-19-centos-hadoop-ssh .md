---
layout: post
title: CentOs7.3 Hadoop 用户 ssh 免密登录
categories: Linux
description: CentOs7.3 Hadoop ssh 免密登录
keywords: Linux
---

## 环境

三台虚拟机(IP)：
 - 192.168.252.121
 - 192.168.252.122
 - 192.168.252.123


## 1.修改主机名

修改三台主机名，以此类推，node1，node3，node3

命令格式

```sh
hostnamectl set-hostname <hostname>
```
```sh
sudo hostnamectl set-hostname node1
```
剩下的虚拟机依次修改`hostnamectl set-hostname[1-3]`


**重启操作系统**
```sh
$ reboot
```

## 2.修改映射关系

1.在 node1 的 `/etc/hosts` 文件下添加如下内容

```sh
su root
vi /etc/hosts
```

2.查看修改后的`/etc/hosts` 文件内容

```sh
$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
# 以下是添加的
192.168.252.121 node1
192.168.252.122 node2
192.168.252.123 node3
```

2.将集群node1 上的文件`hosts`文件 通过 `scp` 命令复制发送到集群的每一个节点

```sh
for a in {1..3} ; do scp /etc/hosts node$a:/etc/hosts ; done
```

3.检查是否集群每一个节点的 `hosts` 文件都已经修改过来了

```sh
for a in {1..3} ; do ssh node$a cat /etc/hosts ; done
```


## 3.启动 ssh 无密登录

1.在集群node1的 `/etc/ssh/sshd_config ` 文件去掉以下选项的注释

```sh
vi /etc/ssh/sshd_config 
```

```sh
RSAAuthentication yes      #开启私钥验证
PubkeyAuthentication yes   #开启公钥验证
```

2.将集群node1 修改后的 `/etc/ssh/sshd_config ` 通过 `scp` 命令复制发送到集群的每一个节点

```sh
for a in {1..3} ; do scp /etc/ssh/sshd_config node$a:/etc/ssh/sshd_config ; done
```

## 4.生成公钥、私钥

1.在集群的每一个节点节点输入命令 `ssh-keygen -t rsa -P ''`，生成 key，一律回车

```sh
su hadoop
ssh-keygen -t rsa -P ''
```

```sh
Generating public/private rsa key pair.
Enter file in which to save the key (/home/hadoop/.ssh/id_rsa): 
Created directory '/home/hadoop/.ssh'.
Your identification has been saved in /home/hadoop/.ssh/id_rsa.
Your public key has been saved in /home/hadoop/.ssh/id_rsa.pub.
The key fingerprint is:
aa:be:0e:46:9a:e8:d5:dc:79:ea:5a:b8:9b:08:e2:dd hadoop@node2
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|                 |
|                 |
|  .     S        |
|.+  o o..        |
|=.o. +.+ .       |
|+.+.o.+ o        |
| o ==E+o         |
+-----------------+

```

2.在集群的node1 节点输入命令

将集群每一个节点的公钥`id_rsa.pub`放入到自己的认证文件中`authorized_keys`;

```sh
for a in {1..3}; do sudo ssh hadoop@node$a cat /home/hadoop/.ssh/id_rsa.pub >> /home/hadoop/.ssh/authorized_keys; done
```

3.在集群的node1 节点输入命令

将自己的认证文件 `authorized_keys` ` 通过 `scp` 命令复制发送到每一个节点上去: `/home/hadoop/.ssh/authorized_keys`

```sh
for a in {1..3}; do sudo scp /home/hadoop/.ssh/authorized_keys hadoop@node$a:/home/hadoop/.ssh/authorized_keys ; done
```

4.非ROOT 用户需赋权限
```sh
chmod 700 /home/hadoop/.ssh/
chmod 700 /home/hadoop/
chmod 600 /home/hadoop/.ssh/authorized_keys 
```

5.在集群的每一个节点节点输入命令

接重启ssh服务

```sh
sudo systemctl restart sshd.service
```

## 6.验证 ssh 无密登录

开一个其他窗口测试下能否免密登陆

例如：在node3

```sh
ssh hadoop@node2
```

`exit` 退出
```sh
[hadoop@node1 ~]# exit
logout
Connection to node1 closed.
```

注意：开新的其他窗口测试下能否免密登陆，把当前窗口都关了


# Contact

 - 作者：鹏磊  
 - 出处：[http://www.ymq.io](http://www.ymq.io)  
 - Email：[admin@souyunku.com](admin@souyunku.com)
 - 版权归作者所有，转载请注明出处
 - Wechat：关注公众号，搜云库，专注于开发技术的研究与知识分享
 
![关注公众号-搜云库](http://www.ymq.io/images/souyunku.png "搜云库")