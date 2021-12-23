安装Oracle依赖包
```shell
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

检查是否安装成功
```shell
rpm -q bc binutils compat-libcap1 compat-libstdc++-33 gcc gcc-c++ elfutils-libelf elfutils-libelf-devel glibc glibc-devel ksh libaio libaio-devel libgcc libstdc++ libstdc++-devel libxcb libX11 libXau libXi libXtst libXrender libXrender-devel make net-tools nfs-utils smartmontools sysstat e2fsprogs e2fsprogs-libs fontconfig-devel expect unzip openssh-clients readline
```
Linux7需要手动安装compat-libstdc++依赖包：

```shell
rpm -ivh compat-libstdc++-33-3.2.3-72.el7.x86_64.rpm
```
```shell

mount  -o loop  /soft/rhel5.3x64.iso  /mnt 

[rhel-server]
name=Red Hat Enterprise Linux server
baseurl=file:///media/Server
enabled=1
gpgcheck=0
#gpgkey=file:///media/OL5.8 x86_64 dvd 20120229/Server


yum clean all 
```

```shell
Oracle 11g 86%报错：Error in invoking target 'agent nmhs' of makefile


cd $ORACLE_HOME/sysman/lib

cp ins_emagent.mk ins_emagent.mk.bak

#修改$ORACLE_HOME/sysman/lib/ins_emagent.mk，将
#$(MK_EMAGENT_NMECTL)修改为：$(MK_EMAGENT_NMECTL) -lnnz11

vi ins_emagent.mk
#进入vi编辑器后  命令模式输入/NMECTL 进行查找，快速定位要修改的行
#在后面追加参数-lnnz11        第一个是字母l   后面两个是数字1
```

10、配置系统参数文件

```shell
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

