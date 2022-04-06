# 前言

相信大家第一次接触 Oracle，大都是在 Windows 上安装，比较方便快捷，基本上是一直下一步就可以安装成功。然而企业级的数据库，基本上都是安装在 Linux 服务器上，安全且高效。

**没接触过 Linux 的朋友不用害怕，跟着本篇文章一步步操作，安装 Oracle 如喝水般简单且标准。**

下面我就来手把手教大家如何在 Linux 上安装 Oracle 数据库。![Image](assets/640.webp)

# 一、前期准备

## 1、虚拟机安装包

**百度网盘下载链接：**

```
https://pan.baidu.com/s/15v8kYgPzmjV-ieIFI4hSzQ
```

## 2、Oracle软件安装包

**百度网盘下载链接：**

```
https://pan.baidu.com/s/1GIw7T32PGEhfu4FFihA9pg
```

## 3、Linux系统安装包

一般有三种Linux系统比较常用：**「RedHat」** 、 **「OracleLinux」** 、**「Centos」** 。

**百度网盘下载链接：**

```
https://pan.baidu.com/s/1g-bqeHyfqz917tC_RsXkig
```

## 4、Linux远程连接工具

本文将使用XShell和Xftp工具，安装包可以在官网下载，也可私信博主获取。

其他工具也可以，比如：putty，SecureCRT 等等工具。

**这篇文章演示RedHat Linux 7.6 版本安装 Oracle 11GR2 版本数据库。**

# 二、Linux主机配置

安装 `Linux` 操作系统的步骤可参考：[VMware 虚拟机安装 Linux 系统](http://mp.weixin.qq.com/s?__biz=Mzg5MzcwNzQ0MQ==&mid=2247492161&idx=1&sn=1e164114f7bbaa58a0105f0e19184d9a&chksm=c028179ff75f9e890dd705c2ead642b37a11d4fb1930f8f21a269850a6d53aedeee3d8c6da69&scene=21#wechat_redirect)

主机内存 2G ，硬盘 50G 即可。

使用XShell工具连接Linux主机root用户：

<img src="assets/640-16401580676193.webp" alt="Image" style="zoom:67%;" />

## 1、主机名配置

如果安装时没有配置主机名，或者想要修改主机名，可以通过以下命令修改：

```
hostnamectl set-hostname orcl
```

<img src="assets/640-16401580676194.webp" alt="Image" style="zoom:67%;" />

## 2、网络配置

如果安装时没有配置网络，或者想要修改网络，可以通过以下命令修改：

```
nmcli connection modify eth0 ipv4.addresses 10.211.55.188/24 ipv4.gateway 10.211.55.1 ipv4.method manual autoconnect yes
nmcli connection up eth0
```

<img src="assets/640-16401580676195.webp" alt="Image" style="zoom:67%;" />

## 3、配置Hosts文件

根据上面配置好的主机名和IP，配置hosts文件：

```
cat <<EOF >>/etc/hosts
##OracleBegin##
##Public IP
10.211.55.188     orcl
##OracleEnd##
EOF
```

<img src="assets/640-16401580676206.webp" alt="Image" style="zoom:67%;" />

## 4、防火墙配置

```
systemctl stop firewalld
systemctl disable firewalld
```

<img src="assets/640-16401580676207.webp" alt="Image" style="zoom:67%;" />

## 5、Selinux配置

selinux修改后需要重启主机生效：

```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

<img src="assets/640-16401580676208.webp" alt="Image" style="zoom:67%;" />

## 6、ISO镜像源配置

**📢 注意：**需要先挂载主机镜像！

VMware Workstation 挂载 Linux 镜像：

<img src="assets/640-16401580676209.webp" alt="Image" style="zoom:67%;" />

```
mount /dev/cdrom /mnt
cat <<EOF>/etc/yum.repos.d/local.repo
[local]
name=local
baseurl=file:///mnt
gpgcheck=0
enabled=1
EOF
```

<img src="assets/640-164015806762010.webp" alt="Image" style="zoom:67%;" />

## 7、安装Oracle依赖包

如下依赖包从Oracle官方文档推荐获取：

```
yum install -y bc \
binutils \
compat-libcap1 \
compat-libstdc++-33 \
gcc \
gcc-c++ \
elfutils-libelf \
elfutils-libelf-devel \
glibc \
glibc-devel \
ksh \
libaio \
libaio-devel \
libgcc \
libstdc++ \
libstdc++-devel \
libxcb \
libX11 \
libXau \
libXi \
libXtst \
libXrender \
libXrender-devel \
make \
net-tools \
nfs-utils \
smartmontools \
sysstat \
e2fsprogs \
e2fsprogs-libs \
fontconfig-devel \
expect \
unzip \
openssh-clients \
readline* \
psmisc --skip-broken
```

检查是否安装成功：

```
rpm -q bc binutils compat-libcap1 compat-libstdc++-33 gcc gcc-c++ elfutils-libelf elfutils-libelf-devel glibc glibc-devel ksh libaio libaio-devel libgcc libstdc++ libstdc++-devel libxcb libX11 libXau libXi libXtst libXrender libXrender-devel make net-tools nfs-utils smartmontools sysstat e2fsprogs e2fsprogs-libs fontconfig-devel expect unzip openssh-clients readline
```

<img src="assets/640-164015806762111.webp" alt="Image" style="zoom:67%;" />

Linux7需要手动安装compat-libstdc++依赖包：

```
rpm -ivh compat-libstdc++-33-3.2.3-72.el7.x86_64.rpm
```

<img src="assets/640-164015806762112.webp" alt="Image" style="zoom:67%;" />

## 8、配置ZeroConf

```
##关闭Zeroconf service的服务守护进程
systemctl stop avahi-daemon.socket
systemctl stop avahi-daemon.service
systemctl disable avahi-daemon.service
systemctl disable avahi-daemon.socket

##关闭NOZEROCONF
cat <<EOF >>/etc/sysconfig/network
#OracleBegin
NOZEROCONF=yes
#OracleEnd
EOF
```

<img src="assets/640-164015806762113.webp" alt="Image" style="zoom:67%;" />

## 9、关闭透明大页和numa

```
sed -i 's/quiet/quiet transparent_hugepage=never numa=off/' /etc/default/grub
grub2-mkconfig -o /boot/grub2/grub.cfg
```

<img src="assets/640-164015806762114.webp" alt="Image" style="zoom:67%;" />

## 10、配置系统参数文件

```
##计算shmall和shmmax值
memTotal=$(grep MemTotal /proc/meminfo | awk '{print $2}')
totalMemory=$((memTotal / 2048))
shmall=$((memTotal / 4))
if [ $shmall -lt 2097152 ]; then
  shmall=2097152
fi
shmmax=$((memTotal * 1024 - 1))
if [ "$shmmax" -lt 4294967295 ]; then
  shmmax=4294967295
fi
echo $shmall
echo $shmmax

##配置系统参数
cat <<EOF >>/etc/sysctl.conf
#OracleBegin
##shmmal's Calculation formula: physical memory 8G：(8*1024*1024*1024)/4096=2097152
##shmmax's Calculation formula: physical memory 8G：(8/2)*1024*1024*1024 -1=4294967295
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.shmall = $shmall
kernel.shmmax = $shmmax
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
EOF

##系统参数生效
sysctl -p
```

<img src="assets/640-164015806762115.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762116.webp" alt="Image" style="zoom:67%;" />

## 11、配置系统资源限制

```
cat <<EOF >>/etc/security/limits.conf
#OracleBegin
oracle soft nofile 1024
oracle hard nofile 65536
oracle soft stack 10240
oracle hard stack 32768
oracle soft nproc 2047
oracle hard nproc 16384
oracle hard memlock 134217728
oracle soft memlock 134217728
#OracleEnd
EOF

cat <<EOF >>/etc/pam.d/login
#OracleBegin
session required pam_limits.so 
session required /lib64/security/pam_limits.so
#OracleEnd
EOF
```

<img src="assets/640-164015806762217.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762218.webp" alt="Image" style="zoom:67%;" />

## 12、创建用户和组

```
/usr/sbin/groupadd -g 54321 oinstall
/usr/sbin/groupadd -g 54322 dba
/usr/sbin/groupadd -g 54323 oper

/usr/sbin/useradd -u 54321 -g oinstall -G dba,oper oracle
echo oracle | passwd --stdin oracle
```

<img src="assets/640-164015806762219.webp" alt="Image" style="zoom:67%;" />

## 13、创建Oracle安装目录

```
mkdir -p /u01/app/oracle/product/11.2.0/db
mkdir -p /u01/app/oraInventory
mkdir -p /oradata
chown -R oracle:oinstall /oradata
chown -R oracle:oinstall /u01/app
chmod -R 775 /u01/app
```

<img src="assets/640-164015806762220.webp" alt="Image" style="zoom:67%;" />

## 14、配置用户环境变量

```
cat <<EOF >>/home/oracle/.bash_profile
################OracleBegin#########################
umask 022
export TMP=/tmp
export TMPDIR=\$TMP
export NLS_LANG=AMERICAN_AMERICA.AL32UTF8
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=/u01/app/oracle/product/11.2.0/db
export ORACLE_HOSTNAME=orcl
export ORACLE_TERM=xterm
export TNS_ADMIN=\$ORACLE_HOME/network/admin
export LD_LIBRARY_PATH=\$ORACLE_HOME/lib:/lib:/usr/lib
export ORACLE_SID=orcl
export PATH=/usr/sbin:\$PATH
export PATH=\$ORACLE_HOME/bin:\$ORACLE_HOME/OPatch:\$PATH
alias sas='sqlplus / as sysdba'
export PS1="[\`whoami\`@\`hostname\`:"'\$PWD]\$ '
EOF
```

<img src="assets/640-164015806762221.webp" alt="Image" style="zoom:67%;" />

# 三、Oracle软件安装

## 1、Oracle软件包上传

```
[root@orcl soft]# ll
-rw-r--r--. 1 root root 1395582860 May 31 16:56 p13390677_112040_Linux-x86-64_1of7.zip
-rw-r--r--. 1 root root 1151304589 May 31 16:56 p13390677_112040_Linux-x86-64_2of7.zip
```

![Image](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

## 2、解压Oracle软件安装包

需要按顺序解压1，2安装包：

```
cd /soft
unzip -q p13390677_112040_Linux-x86-64_1of7.zip
unzip -q p13390677_112040_Linux-x86-64_2of7.zip

##授权/soft给oracle读写权限
chown -R oracle:oinstall /soft
```

<img src="assets/640-164015806762222.webp" alt="Image" style="zoom:67%;" />

## 3、安装VNC软件

```
yum install -y tigervnc*

su - oracle
vncserver
##输入密码
```

<img src="assets/640-164015806762323.webp" alt="Image" style="zoom:67%;" />

## 4、连接VNC远程工具或者直接打开虚拟机图形化界面

<img src="assets/640-164015806762324.webp" alt="Image" style="zoom:67%;" />

右键打开终端工具：

<img src="assets/640-164015806762425.webp" alt="Image" style="zoom:67%;" />

进入 /soft/database 开始安装 Oracle 软件：

```
./runInstaller -jreLoc /etc/alternatives/jre_1.8.0
```

<img src="assets/640-164015806762426.webp" alt="Image" style="zoom:67%;" />

不接收 Oracle 邮件推送：

<img src="assets/640-164015806762427.webp" alt="Image" style="zoom:67%;" />

不更新 Oracle：

<img src="assets/640-164015806762528.webp" alt="Image" style="zoom:67%;" />

只安装 Oracle 软件：

<img src="assets/640-164015806762529.webp" alt="Image" style="zoom:67%;" />

单实例安装：

<img src="assets/640-164015806762530.webp" alt="Image" style="zoom:67%;" />

选择企业版：

<img src="assets/640-164015806762531.webp" alt="Image" style="zoom:67%;" />

选择安装目录：

<img src="assets/640-164015806762632.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762633.webp" alt="Image" style="zoom:67%;" />

选择安装用户组：

<img src="assets/640-164015806762634.webp" alt="Image" style="zoom:67%;" />

Oracle 安装前必要检查：

<img src="assets/640-164015806762635.webp" alt="Image" style="zoom:67%;" />

上传 pdksh-5.2.14-37.el5.x86_64.rpm 依赖包并安装：

```
rpm -e ksh-20120801-142.el7.x86_64
rpm -ivh pdksh-5.2.14-37.el5.x86_64.rpm
```

<img src="assets/640-164015806762636.webp" alt="Image" style="zoom:67%;" />

点击再次检查，忽略 Swap 警告：

<img src="assets/640-164015806762737.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762738.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762739.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762740.webp" alt="Image" style="zoom:67%;" />

解决方案：

```
su - oracle
sed -i 's/^\(\s*\$(MK_EMAGENT_NMECTL)\)\s*$/\1 -lnnz11/g' $ORACLE_HOME/sysman/lib/ins_emagent.mk
```

执行完点击retry重试：

<img src="assets/640-164015806762741.webp" alt="Image" style="zoom:67%;" />

执行 root 脚本：

<img src="assets/640-164015806762742.webp" alt="Image" style="zoom:67%;" />

root用户下执行脚本：

```
/u01/app/oraInventory/orainstRoot.sh
/u01/app/oracle/product/11.2.0/db/root.sh
```

<img src="assets/640-164015806762743.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762844.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806762845.webp" alt="Image" style="zoom:67%;" />

安装完成后，重启主机。

# 四、创建数据库

## 1、打开监听

```
su - oracle
lsnrctl start
lsnrctl status
```

<img src="assets/640-164015806762846.webp" alt="Image" style="zoom: 67%;" />

## 2、连接VNC远程工具或者直接打开虚拟机图形化界面

```
dbca
```

<img src="assets/640-164015806762847.webp" alt="Image" style="zoom:67%;" />

创建数据库：

<img src="assets/640-164015806762848.webp" alt="Image" style="zoom:67%;" />

选择自定义模式：

<img src="assets/640-164015806762849.webp" alt="Image" style="zoom:67%;" />

输入实例名：

<img src="assets/640-164015806762850.webp" alt="Image" style="zoom:67%;" />

这里填写数据库实例名称和 dbname，本次填写 orcl。

<img src="assets/640-164015806762851.webp" alt="Image" style="zoom:67%;" />

不安装EM工具。

<img src="assets/640-164015806762952.webp" alt="Image" style="zoom:67%;" />

这里输入SYS和SYSTEM用户的密码，需要记住。

<img src="assets/640-164015806762953.webp" alt="Image" style="zoom:67%;" />

这里选择前面建好的/oradata目录用来存放数据文件。

<img src="assets/640-164015806762954.webp" alt="Image" style="zoom:67%;" />

不开启闪回日志，不开启归档日志，可以建好库之后再手动修改。

<img src="assets/640-164015806763055.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806763056.webp" alt="Image" style="zoom:67%;" />

数据库内存分配，选择手动分配，占用物理内存70%左右。

<img src="assets/640-164015806763057.webp" alt="Image" style="zoom:67%;" />

block_size根据实际情况选择，一旦建库无法修改，默认8K。

<img src="assets/640-164015806763058.webp" alt="Image" style="zoom:67%;" />

字符集根据需要进行选择，默认AL32UTF8。

<img src="assets/640-164015806763059.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806763060.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806763161.webp" alt="Image" style="zoom:67%;" />

<img src="assets/640-164015806763162.webp" alt="Image" style="zoom: 67%;" />

等待建库完成即可。

<img src="assets/640-164015806763163.webp" alt="Image" style="zoom:67%;" />

# 五、连接数据库

确保监听正常启动，并监听数据库：

<img src="assets/640-164015806763164.webp" alt="Image" style="zoom:67%;" />

## 1、通过数据库主机连接

```
su - oracle
sqlplus / as sysdba
select sysdate from dual;

##创建数据库用户
create user test identified by test;
grant dba to test;
conn test/test

##创建表
create table test (id number not null,name varchar2(100));
insert into test values (1,'lucifer');
commit;
```

## 2、通过PL/SQL连接test用户

<img src="assets/640-164015806763165.webp" alt="Image" style="zoom: 50%;" />

<img src="assets/640-164015806763166.webp" alt="Image" style="zoom:50%;" />

**至此，Oracle数据库已经安装完毕。**

## 1.1.  数据库安装后的配置

让oracle服务随系统开机自动启动：

（1）  在/etc/rc.d/rc.local中touch下面添加下面三条脚本：

su oracle –lc "/u01/app/product/11.1.0/dbhome_1/bin/lsnrctl start" 

su oracle -lc /u01/app/product/11.1.0/dbhome_1/bin/dbstart 

su oracle –lc "emctl start dbconsole"

（2）  修改/etc/oratab文件在文件中找到orcl:/u01/app/product/11.1.0/dbhome_1:N  修改为:orcl:/u01/app/product/11.1.0/dbhome_1:Y

（3） 执行 dbstart和dbshut会提示：

ORACLE_HOME_LISTNER is not SET, unable to auto-start Oracle Net Listener

Usage: /u01/app/ product/10.2.0/db_1/bin/dbstart ORACLE_HOME

 

错误原因是：dbstart和dbshut脚本文件中ORACLE_HOME_LISTNER的设置有问题，分别打开两个文件找到：ORACLE_HOME_LISTNER=$1,修改为

ORACLE_HOME_LISTNER=$ORACLE_HOME

dbstart 和 dbshut两个文件在/u01/app/product/11.1.0/dbhome_1/bin目录下

重启机器看看oracle服务能不能自动启动

**注意：****oracle11g****默认的密码保存时间为****180****天，以下过程为清除密码时间限制的步骤：**

**简化版本**

1.管理员登陆 

   sqlplus sys/compaq123 as sysdba;

2.修改密码过期时间

   alter profile default limit password_life_time unlimited;

**若暂时未过期，无需执行第三部：**

3.重置密码

   alter user kydbo_zz identified by kydbo_zz;

 

**原版本**

以下假设当前用户是kydbo_zz,原密码也是kydbo_zz

\1.   以sys账户登录

sqlplus sys/compaq123 as sysdba;

![img](assets/clip_image001.png)

2.查看该用户的profile

select username,profile from dba_users where username = 'KYDBO_ZZ';

![img](assets/clip_image002.png)

由上叙查询结果可知，该用户对应的profile 为 ‘DEFAULT’

3 查看该用户的账户限制

select * from dba_profiles where profile = 'DEFAULT' and resource_name = 'PASSWORD_LIFE_TIME';

![img](assets/clip_image004.jpg)

\4. 更改账户限制

alter profile default limit password_life_time unlimited;

\5. 重置该用户的登录密码

alter user kydbo_zz identified by kydbo_zz

**关闭****ORACLE11G****空表不创建****segment****的功能。**

Oracle11g默认对空表不分配segment，故使用exp导出Oracle11g数据库时，空表不会导出。

设置deferred_segment_creation 参数为FALSE后，无论是空表还是非空表，都分配segment。

  在sqlplus中，执行如下命令：

  SQL>alter system set deferred_segment_creation=false;

  查看：

  SQL>show parameter deferred_segment_creation;

  该值设置后只对后面新增的表产生作用，对之前建立的空表不起作用

**Oracle****数据库从11g开始区分密码大小写，在创建新用户时会因为此问题导致新建用户无法正常登录、使用**

可通过修改系统参数使oracle数据库不区分密码大小写，使用system用户登录数据库执行查看sec_case_sensitive_logon值

SQL>show parameter sec_case_sensitive_logon;

NAME             TYPE    VALUE

------------------------------------ -----------

sec_case_sensitive_logon    boolean   TRUE

从查询结果得知sec_case_sensitive_logon值为true,true代表区分大小写

修改系统参数，将sec_case_sensitive_logon值修改为false

SQL>alter system set sec_case_sensitive_logon=false

NAME              TYPE    VALUE

------------------------------------ -----------

sec_case_sensitive_logon    boolean  FALSE

修改完毕后查询得知sec_case_sensitive_logon值为false，false代表不区分大小写，修改完毕后重启数据库