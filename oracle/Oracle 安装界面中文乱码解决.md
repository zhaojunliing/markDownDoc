# oracle安装界面中文乱码解决

在安装oracle时如果我们用的是英文安装没有任何问题，但是我要安装中文的，结果中文界面就出现了乱码了，后来网上找了原因是要安装中文包才可以，下面我来介绍一下。

在Linux的X window里安装oracle，弹出的oracle界面为乱码（方块）原因：oracle安装默认没有中文语言包，只有用英文了。

**解决：**

1、临时解决： 

```shell
$ export LANG=en_US
```

2、永久解决：

```shell
# vi /etc/sysconfig/i18nLANG="en_US"（不会出现乱码） LANG="zh_cn.UTF-8"（中文，安装oracle会出现界面乱码的现象）
```

linux安装Oracle安装界面乱码解决方法！

```shell
export NLS_LANG=AMERICAN_AMERICA.UTF8
export LC_ALL=C
```

如果你没有安装中文字体我们可百度去下载一个，然后把中文字体按下面方法放置

**解决ORACLE安装界面中文乱码, 可以安装本字体;**

安装方法, 将文件解压出来, copy到 /usr/share/fonts/zh_CN/TrueType/

下, 如果没有该目录, 就自己创建 mkdir -p /usr/share/fonts/zh_CN/TrueType/

**总结**

安装英文LINUX环境,安装ORACLE就界面就不乱了,如果你想要使用中文的LINUX,在安装中文字库就可以实现中文的环境.