# RHEL 6.6下Python 2.6.6升级到Python 3.6.6

```shell
yum -y install gcc
yum -y install zlib*
yum -y install openssl-devel 
```

**1：检查当前Linux版本&Python版本信息**

```
# more /etc/redhat-release 
Red Hat Enterprise Linux Server release 6.6 (Santiago)
 
# python -V
Python 2.6.6
```

**2：下载、解压Python安装包**

注意官方网址提供各个版本，以及不同压缩格式的安装包，选择合适自己的即可。如果Linux不能访问外网，也可以手工下载上传。此处略过！

```
#wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6.tgz
#tar -xzvf Python-3.6.6.tgz 
```

**3：编译安装Python 3.6.6**

```
#cd Python-3.6.6
#./configure --prefix=/usr/local --enable-shared --with-ssl
./configure --enable-optimizations
# make 
# make install  或者  make altinstall
# make clean
```

注释：

./configure 是用来检测你的安装平台的目标特征的。比如它会检测你是不是有CC或GCC，并不是需要CC或GCC，它是个shell脚本

make 是用来编译的，它从Makefile中读取指令，然后编译。

make install是用来安装的，它也从Makefile中读取指令，安装到指定的位置。

make clean：清除编译产生的可执行文件及目标文件(object file，*.o)。

 

注意make install 与 make altinstall的区别：

 

install 除了做 altinstall 外还会做 bininstall , maninstall 共三个动作bininstall 就是在处理 Symbolic Link Chain 的相关事务, 而 maninstall 则是在产生 unversioned manual pages, 所以, 很明显的, 不使用 bininstall 可以避免 python install 时 update link 的问题。如果使用make install，你将会看到在系统中有两个不同版本的Python在/usr/bin/目录中。这将会导致很多问题，而且不好处理。

 

 

**4：验证是否升级成功**

```
# python -V
Python 2.6.6
# /usr/local/bin/python3.6 -V
Python 3.6.6
```

**5：设置环境变量**

```
# mv /usr/bin/python    /usr/bin/python.bak
# ln -s /usr/local/bin/python3.6 /usr/bin/python
# python -V
Python 3.6.6
```

**6：配置yum**

如下所示，升级 Python 之后，由于将默认的python指向了python 3。6，yum不能正常使用，需要编辑 yum 的配置文件

yum clean

 File "/usr/bin/yum", line 30

  except KeyboardInterrupt, e:

​              ^

SyntaxError: invalid syntax

编辑vim /usr/bin/yum 配置文件，将第一行记录#!/usr/bin/python改为#!/usr/bin/python2.6

**7:使用pyinstaller报错**

```shell
python3: error while loading shared libraries: libpython3.6m.so.1.0: cannot open shared object file: No such file or directory
```

```shell
[root@localhost]# ldd /usr/local/bin/python3
	linux-vdso.so.1 =>  (0x00007ffeb55f3000)
	libpython3.6m.so.1.0 => not found
	libpthread.so.0 => /lib64/libpthread.so.0 (0x0000003ead200000)
	libdl.so.2 => /lib64/libdl.so.2 (0x0000003eaca00000)
	libutil.so.1 => /lib64/libutil.so.1 (0x0000003ebba00000)
	librt.so.1 => /lib64/librt.so.1 (0x0000003ead600000)
	libm.so.6 => /lib64/libm.so.6 (0x0000003eada00000)
	libc.so.6 => /lib64/libc.so.6 (0x0000003eace00000)
	/lib64/ld-linux-x86-64.so.2 (0x0000003eac600000)
# libpython3.6m.so.1.0查找不到
```

查找一下文件的位置：

```shell
[root@localhost]# find / -name 'libpython3.6m.so.1.0'
/root/Python-3.6.6/libpython3.6m.so.1.0
/usr/local/lib/libpython3.6m.so.1.0
```

/etc/ld.so.conf.d 下，建立 python3.conf

并在 python3.conf 中加入：也就是所在的目录

/usr/local/lib/

然后运行：ldconfig

再运行python3 就可以啦

这里面用到了 /etc/ld.so.conf 这个文件，还不是很明白，有空再去理解一下。