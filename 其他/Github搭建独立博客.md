# Github搭建独立博客
id: github-blog
title: Github搭建独立博客
date: 2014-01-10 13:39:36
tags: [Github,Hexo]
---
春眠不觉晓，处处问题不少。才疏学浅，资质愚钝，还不刻苦努力，荒废了人生，虚度了年华，从IT男变成了IT单身狗。在开发过程中遇到的问题，翻着墙用着Goolge搜索，忽然之间很多以github.io的博客如风雨过后菊花开般崛起。冒着好奇害死猫的风险开始探个究竟，果然入坑了。

## 准备
1. [Git 下载](http://git-scm.com/)

2. [Node.js 下载](http://nodejs.org/) 版本：Windows Installer (.msi)

忘了告诉你，这里是在Windows环境下的搭建，我的Mac一直在店里没去拿。

<!-- more --> 

## 安装软件
1. 安装Git，按步骤默认安装就好，安装完后在开始菜单中会有Git的目录

2. 同样安装好Node.js，安装完后在开始菜单中会有Node.js的目录


## 注册Github
访问：[https://www.github.com/](https://www.github.com/)

注册你的username和邮箱，邮箱十分重要，GitHub上很多通知都是通过邮箱的。

## 创建仓库（repository）
需要特别注意的就是Repository name，必须为username.github.io(useranem是指你的Github用户名)，不然不能被github解析。

## 配置Git
在Windows下，开始菜单中找到Git目录打开Git Bash进入命令行界面。

### 检查SSH keys的设置
首先我们需要检查你电脑上现有的ssh key：

    $ cd ~/.ssh
如果显示“No such file or directory”，跳过下一步，否则继续。

### 备份和移除原来的ssh key设置
发现已经存在ssh key，在Git Bash中输入以下命令：

    $ ls
    $ config  id_rsa  id_rsa.pub  known_hosts
    $ mkdir key_backup
    $ cp id_rsa* key_backup
    $ rm id_rsa*

### 生成新的SSH Key
输入以下命令：

    $ ssh-keygen -t rsa -C "邮件地址@youremail.com"

命令行界面会出现：

    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):

这时回车就可以了，然后系统会要你输入加密串

    Enter passphrase (empty for no passphrase):<输入加密串>
    Enter same passphrase again:<再次输入加密串>

最后看到这样的界面，就成功设置ssh key了：

    Your identifiaction has ban save in
    省略一万字

在Windows下，进入系统盘的当前用户目录会看到.ssh目录，其中有一个叫id_rsa.pub的文件，如果看不到请设置显示隐藏文件。

### 添加SSH Key到GitHub


1. 设置SSH Key之后，需要添加到GitHub上，以完成SSH链接的设置。

2. 登陆GitHub，点击在主页右上角的头像的Setting，找到SSH keys进入。

3. 点击Add SSH key，然后输入Title和Key的内容：Title是自定义，不重复即可；Key则是id_ras.pub用记事本打开，负责全部内容填入。

4. 点击下方的Add key添加完成。


### 测试
在添加完成后，输入以下命令：

    ssh -T git@github.com

如果是下面的反应：

    The authenticity of host 'github.com (207.97.227.239)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?

输入yes并回车

    Hi <em>username</em>! You've successfully authenticated, but GitHub does not provide shell access.

说明已经成功的设置好了SSH Key。

### 设置账号信息
现在你已经可以通过SSH链接到GitHub了，还有一些个人信息需要完善的。

Git会根据用户的名字和邮箱来记录提交。GitHub也是用这些信息来做权限的处理，输入下面的代码进行个人信息的设置，把名称和邮箱替换成你自己的，名字必须是GitHub的用户名，而不是GitHub的昵称。

    $ git config --global user.name "你的名字"
    $ git config --global user.email "your_email@youremail.com"

OK，这样就可以使用Git将本地目录发布到Github的仓库中了。

## 安装Hexo
Github的博客主页可以使用很多种模板系统，这里用的是Hexo，打开Git Bash命令行界面输入：

    npm install -g hexo

在Windows下建立hexo的目录，我将博客放在了D:/hexo 目录下，按住Shift见在新建的hexo目录上鼠标右击，点击Git Bash进入命令行界面输入：

    hexo init
    npm install

现在本地版本的hexo已经配置完成了，可以使用以下命令来生成静态文件：

    $ hexo g  这是生成静态页面的命令
    $ hexo s  这是启动本地服务器的命令

然后打开浏览器输入 http://localhost:4000 即可看到hexo的主页。

[点击查看Hexo简单笔记](/2014/01/11/hexo-guide)