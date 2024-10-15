---
title: su 认证失败
tags:
  - bug
categories:
  - - bug
  - - Linux Ubuntu
series: Linux
description: hello linux
top_img: false
abbrlink: 6b877bd2
date: 2024-10-15 11:13:00
---
# linux ubuntu 切换到超级用户失败的解决办法（su 认证失败）

##### 错误如下：

![image-20241015105956857](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20241015105956857.png)

##### 解决：

![image-20241015110109336](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20241015110109336.png)

```bash
xsq@ubuntu:~$ su
密码： 
su：认证失败
xsq@ubuntu:~$ su passwd root
用户“passwd”不存在
xsq@ubuntu:~$ sudo passwd root
新的密码：
重新输入新的密码：
passwd：已成功更新密码
xsq@ubuntu:~$ su
密码： 
root@ubuntu:/home/xsq# 
```

##### 原因：

```text
linux su认证失败

Ubuntu安装后，root用户默认是被锁定了的，不允许登录，也不允许 su 到 root ，可以通过更改root的密码来登录root。

su 切换root不行所以就用手动形式进行改密码切换。

sudo passwd root更换密码之后就可以输入 su 登录到root用户了。

su ： 切换到某某用户模式，提示输入密码时该密码为切换后账户的密码，用法为“su 账户名称”。

如果后面不加账户时系统默认为root账户，密码也为超级账户的密码。没有时间限制。

sudo : 暂时切换到超级用户模式以执行超级用户权限，提示输入密码时该密码为当前用户的密码，而不是超级账户的密码。

不过有时间限制，Ubuntu默认为一次时长15分钟。

```

Linux Ubuntu: 解决su认证失败问题
在Linux系统中，尤其是Ubuntu，可能会遇到切换到超级用户（也就是root用户）时出现的认证失败问题。以下将详细讨论解决此类问题的不同方法，并给出相关的命令和代码示例。



**问题背景**
在Ubuntu或其他基于Debian的Linux发行版中，su命令用于切换到不同的用户账户，包括超级用户(root)。然而，在尝试使用su命令切换到root用户时，可能会遇到"Authentication failure"的错误。

```bash
su -
Password:
su: Authentication failure
```

这意味着无法切换到root用户，从而导致无法执行需要超级用户权限的任务。

原因分析
Ubuntu默认情况下，root账户被禁用，因此没有设置密码。由于这个原因，尝试使用su命令并输入用户自己的密码切换到root用户，会返回“认证失败”。

解决方案
**使用sudo**
Ubuntu推荐使用`sudo`命令代替直接使用root用户。在Ubuntu中，`sudo`命令允许普通用户执行具有root权限的命令。例如，如果想要以root用户身份运行`apt-get update`命令，可以这样做：

```bash
sudo apt-get update
```

首次运行sudo命令时，系统会提示输入当前用户的密码。此后，在一段时间内（默认为15分钟）再次使用sudo命令，将不再需要密码。

**更改root密码**
如果确实需要使用root账户，可以通过设置root密码来启用它。这可以通过以下步骤完成：

打开终端。
输入以下命令：

```bash
sudo passwd root
```

系统将提示输入新的UNIX密码。输入新的root密码，然后再次输入以确认。
现在，应该可以使用新设置的密码通过su命令切换到root用户了。

```bash
su -
Password: <Enter the new root password>
```

**创建新的超级用户**
另一个选择是创建一个新的超级用户。这可以通过以下步骤完成：

打开终端。
输入以下命令创建一个新用户（替换newuser为你希望的用户名）：

```bash
sudo adduser newuser
```

输入新用户的密码，然后再次输入以确认。
接下来，将新用户添加到sudo组，使其具有超级用户权限：

```bash
sudo usermod -aG sudo newuser
```

现在，可以使用新创建的超级用户执行需要root权限的命令。

**参考资料**
Ubuntu Documentation, RootSudo. https://help.ubuntu.com/community/RootSudo
Stack Overflow, “su Authentication failure”. https://stackoverflow.com/questions/33598753/su-authentication-failure
注意：在处理涉及管理员权限的问题时，务必谨慎行事。不正确的操作可能会导致系统不稳定或数据丢失。始终确保有适当的备份，并在可能的情况下，首先在非生产环境中测试任何更改。