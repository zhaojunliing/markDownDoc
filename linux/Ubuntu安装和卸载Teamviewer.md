# Ubuntu 安装和卸载Teamviewer

1. 安装依赖包，ternimal终端进入到下载路径中，执行命令：

```shell
sudo apt-get install libjpeg62:i386 libxinerama1:i386 libxrandr2:i386 libxtst6:i386 ca-certificates
```

64位机器也可以不执行

2. 安装deb软件包，执行命令：

```shell
sudo dpkg -i teamviewer_14.1.3399_amd64.deb
或者
apt install ./teamviewer_13.x.yyy_[arch].deb
```

3. 安装成功之后在图形化界面dash输入Teamviewer就可以打开了。

　　注意：在执行第三步安装deb包的时候，可能会遇到下面的问题：

```shell
wanglaotou@wanglaotou95:~/softwares/software-package$ sudo dpkg -i teamviewer_12.0.85001_i386.deb
(正在读取数据库 ... 系统当前共安装有 215790 个文件和目录。)
正准备解包 teamviewer_12.0.85001_i386.deb  ...
正在将 teamviewer:i386 (12.0.85001) 解包到 (12.0.85001) 上 ...
dpkg: 依赖关系问题使得 teamviewer:i386 的配置工作不能继续：
teamviewer:i386 依赖于 libasound2.
teamviewer:i386 依赖于 libdbus-1-3.
teamviewer:i386 依赖于 libexpat1.
teamviewer:i386 依赖于 libfontconfig1.
teamviewer:i386 依赖于 libfreetype6.
teamviewer:i386 依赖于 libsm6.
teamviewer:i386 依赖于 libxdamage1.
teamviewer:i386 依赖于 libxfixes3.
teamviewer:i386 依赖于 zlib1g.
    
dpkg: 处理软件包 teamviewer:i386 (--install)时出错：
  依赖关系问题 - 仍未被配置
正在处理用于 gnome-menus (3.13.3-6ubuntu3.1) 的触发器 ...
正在处理用于 desktop-file-utils (0.22-1ubuntu5.1) 的触发器 ...
正在处理用于 bamfdaemon (0.5.3~bzr0+16.04.20160824-0ubuntu1) 的触发器 ...
Rebuilding /usr/share/applications/bamf-2.index...
正在处理用于 mime-support (3.59ubuntu1) 的触发器 ...
正在处理用于 hicolor-icon-theme (0.15-0ubuntu1) 的触发器 ...
在处理时有错误发生：
   teamviewer:i386
```

　　（下面这个命令是修复依赖关系（depends）的命令，就是假如你的系统上有某个package不满足依赖条件，这个命令就会自动修复，安装那个package依赖的package）

　　这个时候需要执行命令：

~~~shell
sudo apt-get install -f　　

--选择 ‘Y’，回车等待修复结束后继续执行第三步。
~~~

4. 卸载命令

```shell
sudo apt --purge remove teamviewer*
```

   5、命令行配置

```shell
teamviewer help       # list all available commands
teamviewer info       # show TeamViewer ID
teamviewer passwd     # set password
teamviewer setup      # assign device to account
```



