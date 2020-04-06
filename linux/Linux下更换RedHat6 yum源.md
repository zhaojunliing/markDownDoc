# Linux下更换RedHat6 yum源

> 如果你新安装了RedHat6，登录系统使用yum update 更新系统时，会得到如下的提示：

`linux  
This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.  
`

（换成通俗一点的话，那就是：快来注册，你不注册，就不给你用。）

因为RedHat 默认自带的 yum 源需要注册，才能更新,而我们想不花钱也可以更新，那只能替换掉RedHat的yum源了，下面就给大家介绍一下如果更换RedHat的yum源。

**第一步：检查是否安装yum软件包。**

首先，可以用rpm -qa |grep yum 命令来查看RHEL是否安装了yum，如果安装了，就会显示系统安装的那些yum包：

```shell
[root@localhost ~]# rpm -qa |grep yum
PackageKit-yum-0.5.8-21.el6.x86_64
yum-utils-1.1.30-14.el6.noarch
yum-metadata-parser-1.1.2-16.el6.x86_64
PackageKit-yum-plugin-0.5.8-21.el6.x86_64
yum-rhn-plugin-0.9.1-48.el6.noarch
yum-plugin-security-1.1.30-14.el6.noarch
yum-3.2.29-40.el6.noarch
```

**第二步： 卸载redhat自带的yum软件包。**

```shell
[root@localhost ~]# rpm -aq|grep yum|xargs rpm -e --nodeps
```

卸载完成后，再用命令 rpm -qa |grep yum 查看是否已经卸载完成，如果输入命令后，无信息显示表示已经卸载完成：

```shell
[root@localhost ~]# rpm -qa |grep yum
[root@localhost ~]# 
```

**第三步.下载新的yum软件包。**

一般可以进入以下网站上面查看软件包的版本是否升级或者找到自己系统所对应的文件包版本更新；

网易163网络源地址：http://mirrors.163.com/

CentOS网络源地址：http://centos.ustc.edu.cn/centos/

找到自己所需要的版本下载：

```shell
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-urlgrabber-3.9.1-11.el6.noarch.rpm
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.29-81.el6.centos.noarch.rpm
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-utils-1.1.30-41.el6.noarch.rpm
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-updateonboot-1.1.30-41.el6.noarch.rpm
[root@localhost ~]# wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
```

**第四步、安装yum软件包。**

注意:单个的安装包可能会依赖其它包（例如yum和yum-fastestmirror会相互依赖），所以我们可以把所有这些包放在一起，用一行命令将它们同时安装即可：

```shell
rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
rpm -ivh yum-3.2.29-81.el6.centos.noarch.rpm yum-plugin-fastestmirror-1.1.30-40.el6.noarch.rpm
 
注意最后两个包必需同时安装，否则会相互依赖
```

另外，在最后一步，执行rpm安装yum-3.2.29安装的时候，有了错误提示：

```shell
[root@bogon softwares]# rpm -ivh yum-3.2.29-81.el6.centos.noarch.rpm  yum-plugin-fastestmirror-1.1.30-40.el6.noarch.rpm 
warning: yum-3.2.29-81.el6.centos.noarch.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
error: Failed dependencies:
	python-urlgrabber >= 3.9.1-10 is needed by yum-3.2.29-81.el6.centos.noarch
```

如上，错误信息比较明显，是还缺失了python-urlgrabber，3.9.1-10版本以上的依赖。

使用rpm执行安装：

```shell
rpm -ivh python-urlgrabber-3.9.1-11.el6.noarch.rpm 
```

```shell
warning: python-urlgrabber-3.9.1-11.el6.noarch.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Preparing...                ########################################### [100%]
	file /usr/lib/python2.6/site-packages/urlgrabber/grabber.py from install of python-urlgrabber-3.9.1-11.el6.noarch conflicts with file from package python-urlgrabber-3.9.1-8.el6.noarch
	file /usr/lib/python2.6/site-packages/urlgrabber/grabber.pyc from install of python-urlgrabber-3.9.1-11.el6.noarch conflicts with file from package python-urlgrabber-3.9.1-8.el6.noarch
	file /usr/lib/python2.6/site-packages/urlgrabber/grabber.pyo from install of python-urlgrabber-3.9.1-11.el6.noarch conflicts with file from package python-urlgrabber-3.9.1-8.el6.noarch
	file /usr/lib/python2.6/site-packages/urlgrabber/progress.pyc from install of python-urlgrabber-3.9.1-11.el6.noarch conflicts with file from package python-urlgrabber-3.9.1-8.el6.noarch
	file /usr/lib/python2.6/site-packages/urlgrabber/progress.pyo from install of python-urlgrabber-3.9.1-11.el6.noarch conflicts with file from package python-urlgrabber-3.9.1-8.el6.noarch
```

坑爹啊。。好在是有conflicts with的提示，表明是与历史版本冲突了，在rpm安装的命令中加入--force 强制安装尝试，安装成功：

```html
rpm -ivh --force python-urlgrabber-3.9.1-11.el6.noarch.rpm 
warning: python-urlgrabber-3.9.1-11.el6.noarch.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Preparing...                ########################################### [100%]
   1:python-urlgrabber      ########################################### [100%]
```

最后，再执行yum-3 的rpm安装，命令和执行成功提示如下：

```html
rpm -ivh yum-3.2.29-81.el6.centos.noarch.rpm  yum-plugin-fastestmirror-1.1.30-40.el6.noarch.rpm 
warning: yum-3.2.29-81.el6.centos.noarch.rpm: Header V3 RSA/SHA1 Signature, key ID c105b9de: NOKEY
Preparing...                ########################################### [100%]
   1:yum-plugin-fastestmirro########################################### [ 50%]
   2:yum                    ########################################### [100%]
```

第五步、新建repo 配置文件。**

```
[base]
name=CentOS-$releasever - Base
baseurl=http://mirrors.163.com/centos/6/os/$basearch/
gpgcheck=1 
gpgkey=http://mirrors.163.com/centos/6/os/x86_64/RPM-GPG-KEY-CentOS-6

#released updates
 
[updates]
name=CentOS-$releasever - Updates
baseurl=http://mirrors.163.com/centos/6/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/6/os/x86_64/RPM-GPG-KEY-CentOS-6
#packages used/produced in the build but not released
 
#[addons]
#name=CentOS-$releasever - Addons
#baseurl=http://mirrors.163.com/centos/6/addons/$basearch/
#gpgcheck=1
#gpgkey=http://mirrors.163.com/centos/6/os/x86_64/RPM-GPG-KEY-CentOS-6
 
#additional packages that may be useful
 
[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirrors.163.com/centos/6/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/6/os/x86_64/RPM-GPG-KEY-CentOS-6
 
#additional packages that extend functionality of existing packages
 
[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://mirrors.163.com/centos/6./centosplus/$basearch/
gpgcheck=1
enabled=0
```

**第六步：运行yum makecache命令生成缓存。**

 ```shell
[root@linuxprobe ~]# yum clean all
[root@linuxprobe ~]# yum makecache
[root@linuxprobe ~]# yum update
 ```

经过上述六个步骤，新的yum源已经安装完成了，我们可以用yum install 命令来测试安装是否正常：

 到这里，新的yum源已经安装完成了，以后我们就可以用yum命令来愉快的使用RedHat系统了，而不必再担心会出现注册提示无法更新的问题了。

参考：https://blog.csdn.net/liuhuoxingkong/article/details/75949704