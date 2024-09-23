---
title: 安装Docker
tags:
  - 环境搭建
  - SpringCloud
categories:
  - - SpringCloud
  - - 环境搭建
series: SpringCloud
description: hello word~
top_img: false
cover: 'https://pic.imgdb.cn/item/64c0ad651ddac507cc99ceb7.jpg'
abbrlink: 29c15ce3
date: 2024-09-03 09:10:27
---

![docker](https://pic.imgdb.cn/item/64c0ad651ddac507cc99ceb7.jpg)

# 1.卸载旧版

首先如果系统中已存在旧的 **Docker** ，则先卸载：

```Shell
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine \
    docker-selinux 
```

# 2.配置 Docker 的 yum 库

首先要安装一个 **yum** 工具

```Bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

安装成功后，执行命令，配置 **Docker** 的 **yum** 源

```Bash
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```

更新 **yum** 建立缓存

```Bash
sudo yum makecache fast
```

# 3.安装 Docker

执行命令，安装 **Docker**

```Bash
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

# 4.启动和校验

```Bash
# 启动Docker
systemctl start docker

# 停止Docker
systemctl stop docker

# 重启
systemctl restart docker

# 设置开机自启
systemctl enable docker

# 执行docker ps命令，如果不报错，说明安装启动成功
docker ps
```

# 5.配置镜像加速

```Bash
# 创建目录
mkdir -p /etc/docker

# 复制内容，注意把其中的镜像加速地址改成你自己的
tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "http://hub-mirror.c.163.com",
        "https://mirrors.tuna.tsinghua.edu.cn",
        "http://mirrors.sohu.com",
        "https://ustc-edu-cn.mirror.aliyuncs.com",
        "https://ccr.ccs.tencentyun.com",
        "https://docker.m.daocloud.io",
        "https://docker.awsl9527.cn"
    ]
}
EOF

# 重新加载配置
systemctl daemon-reload

# 重启Docker
systemctl restart docker
```

