# ubuntu 离线安装 内网安装 无外网安装 离线源

做政府或则企业的项目，通常是本地部署或则私有云，为了防止无外网，或防止现场安装时难以寻找到依赖包的情况发生，需要在开发阶段就准备好所有线上需要的工具或服务，所以需要制作离线包。Ubuntu离线安装软件原理是，在正常有网的**开发服务器**上安装所有依赖内容，并**制作为本地源包**，在**目标服务器**上解压后**映射本地源，进行无外网安装**。

## 一、离线包制作

### 1、配置源

选择的是aliyun源ubuntu 18.04(bionic)

```
mv /etc/apt/sources.list /etc/apt/sources.list.bak
cat >>/etc/apt/sources.list <<EOF
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
EOF
apt-get update
#可不执行
apt-get upgrade
```

### 2、安装离线所需要的所有包

```
apt-get install openssh-server
apt-get install -y gcc
apt-get install -y make
apt-get install -y libc6-dev build-essential
apt-get install -y selinux-utils
apt-get install -y wget
apt-get install -y vim
apt-get install -y curl
apt-get install -y net-tools
apt-get install -y python-minimal
```

### 3、制作离线包

```
cd /var/cache/apt/archives/
dpkg-scanpackages . | gzip -9c > Packages.gz
tar -zcf packages.tar.gz ./*
```

`packages.tar.gz`就是制作好的离线包

## 二、内网安装

### 1、上传离线包

上传之前制作的离线包`packages.tar.gz`

### 2、 建立离线源

```shell
#创建离线文件存放目录
mkdir -p /offline/packages

#解压离线文件
tar -zxvf packages.tar.gz -C /offline/packages/

# 备份源文件
mv /etc/apt/sources.list /etc/apt/sources.list.bak

# 映射本地源
cat >>/etc/apt/sources.list <<EOF
deb [trusted=yes] file:///offline/packages/ ./
EOF

# 更新
apt-get update

```

### 3、安装

```shell
apt-get install openssh-server
apt-get install -y gcc
apt-get install -y make
apt-get install -y libc6-dev build-essential
apt-get install -y selinux-utils
apt-get install -y wget
apt-get install -y vim
apt-get install -y curl
apt-get install -y net-tools
apt-get install -y python-minimal
```

### 4、还原源

```shell
mv /etc/apt/sources.lis.bak /etc/apt/sources.list
```

