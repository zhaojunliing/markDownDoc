### linux LVM逻辑卷的创建,扩容，缩减和删除

#### 什么是LVM？

LVM是逻辑盘卷管理（Logical Volume Manager）的简称，它是Linux环境下对磁盘分区进行管理的一种机制，LVM是建立在硬盘和分区之上的一个逻辑层，来提高磁盘分区管理的灵活性。

#### 为什么要使用LVM？

在日常工作或者学习中，随着电脑的不断被使用，我们的磁盘空间可能会越来越小，这时候，我们不能直接更换大硬盘，因为硬盘有价，数据无价，这时候我们就可以使用LVM，LVM将存储虚拟化,使用逻辑卷,你不会受限于物理磁盘的大小,另外,和硬件相关的存储设置被其隐藏,你能不用停止应用或卸载文件系统来调整卷大小或数据迁移.这样能减少操作成本，LVM最大的特点就是可以对磁盘进行动态管理。因为逻辑卷的大小是可以动态调整的，而且不会丢失现有的数据。如果我们新增加了硬盘，其也不会改变现有上层的逻辑卷。作为一个动态磁盘管理机制，逻辑卷技术大大提高了磁盘管理的灵活性。

#### LVM工作机制？

LVM就是通过将底层的物理硬盘抽象的封装起来，然后以逻辑卷的方式呈现给上层应用。在传统的磁盘管理机制中，我们的上层应用是直接访问文件系统，从而对底层的物理硬盘进行读取，而在LVM中，其通过对底层的硬盘进行封装，当我们对底层的物理硬盘进行操作时，其不再是针对于分区进行操作，而是通过一个叫做逻辑卷的东西来对其进行底层的磁盘管理操作。

#### 逻辑卷管理概念？

 物理卷（PV, Physical Volume）
物理卷就是指磁盘,磁盘分区或从逻辑上和磁盘分区具有同样功能的设备(如RAID)，是LVM的基本存储逻辑块，但和基本的物理存储介质（如分区、磁盘等）比较，却包含有和LVM相关的管理参数。当前LVM允许你在每个物理卷上保存这个物理卷的0至2份元数据拷贝.默认为1,保存在设备的开始处.为2时,在设备结束处保存第二份备份.

卷组（VG, Volume Group）
LVM卷组类似于非LVM系统中的物理硬盘，其由物理卷组成。能在卷组上创建一个或多个“LVM分区”（逻辑卷），LVM卷组由一个或多个物理卷组成。

逻辑卷（LV, Logical Volume）
LVM的逻辑卷类似于非LVM系统中的硬盘分区，在逻辑卷之上能建立文件系统(比如/home或/usr等)。

关系如下：

![pe_vg](assets/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2Jsb2cvMzcwMDQ2LzIwMTQwNi8xNzE0MTMyOTg3OTYzNTYuZ2lm)

具体操作：

1：检查系统中是否安装了LVM管理工具

```shell
rpm -qa|grep lvm
[root@localhost ~]# rpm -qa |grep lvm
lvm2-2.02.177-4.el7.x86_64
llvm-private-5.0.0-3.el7.x86_64
lvm2-libs-2.02.177-4.el7.x86_64
[root@localhost ~]# 
:2：如果未安装，则使用yum 方式安装
# yum install lvm*

# rpm -qa|grep lvm
```

手动添加两个硬盘：sdl 20G sdh 1G

1:先开始创建物理卷PV：

基本PV命令：

 pvcreate　将物理分区新建为pv

    pvs／pvscan　查看系统里有pv的磁盘
    
    pvdisplay　显示系统上面的pv状态
    
    pvremove　删除pv

准备使用:  两个磁盘：/dev/sdl 以及 、/dev/sdh 来完成LVM

```
[root@localhost ~]# pvcreate /dev/sdl /dev/sdh
  Physical volume "/dev/sdl" successfully created.
  Physical volume "/dev/sdh" successfully created.
[root@localhost ~]# 
使用pvs查看新建的PV

[root@localhost ~]# pvs
  PV         VG Fmt  Attr PSize  PFree 
  /dev/sdh      lvm2 ---   1.00g  1.00g
  /dev/sdl      lvm2 ---  20.00g 20.00g
[root@localhost ~]# 
```


2:创建卷组vg

            有关vg的命令：
    
            vgcreate [-s xM] vgName /dev/sd …… 　新建vg，-s后面接pe的大小（可选），单位是M，G，可以放多块pv
    
            vgextend　扩展vg，就是增加pv
    
            vgs／vgscan　查看系统里有vg的磁盘
    
            vgdisplay　显示系统上面的vg状态
    
            vgremove　删除vg
    
            vgreduce　在vg里删除pv

创建vgqjc 并查看：

```
[root@localhost ~]# vgcreate -s 16M vgqjc /dev/sdl
  Volume group "vgqjc" successfully created
[root@localhost ~]# vgs
  VG    #PV #LV #SN Attr   VSize  VFree 
  vgqjc   1   0   0 wz--n- 19.98g 19.98g
[root@localhost ~]# 
```


创建逻辑卷lv

    有关lv的命令：
    lvcreate -l pe num /-L size -n lvname vgName 　新建lv，-l指定pe的个数，-L指定容量，M，G
    lvextend　增加容量
    lvs／lvscan　查看系统里有lv的磁盘
    lvdisplay　显示系统上面的lv状态
    lvremove　删除lv
    lvreduce　在lv里减少容量

创建lvqjc,并查看：

```
[root@localhost ~]# lvcreate -l 512 -n lvqjc vgqjc
WARNING: ext4 signature detected on /dev/vgqjc/lvqjc at offset 1080. Wipe it? [y/n]: y
  Wiping ext4 signature on /dev/vgqjc/lvqjc.
  Logical volume "lvqjc" created.
[root@localhost ~]# lvs
  LV    VG    Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvqjc vgqjc -wi-a----- 8.00g                                                    
[root@localhost ~]# 
```


格式化新建的LV

```
[root@localhost ~]# mkfs.ext4 /dev/vgqjc/lvqjc
mke2fs 1.42.9 (28-Dec-2013)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
524288 inodes, 2097152 blocks
104857 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2147483648
64 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done 

[root@localhost ~]# 
```


挂载使用：

```
[root@localhost ~]# mount /dev/vgqjc/lvqjc /mnt
[root@localhost ~]# df
Filesystem              1K-blocks    Used Available Use% Mounted on
/dev/sda5                20754432 4050424  16704008  20% /
devtmpfs                   483088       0    483088   0% /dev
tmpfs                      498976       0    498976   0% /dev/shm
tmpfs                      498976    8836    490140   2% /run
tmpfs                      498976       0    498976   0% /sys/fs/cgroup
/dev/sda2                10475520  116032  10359488   2% /app
/dev/sda1                  201380  135436     65944  68% /boot
tmpfs                       99796       4     99792   1% /run/user/42
tmpfs                       99796      32     99764   1% /run/user/1000
/dev/sr0                  9176232 9176232         0 100% /run/media/qijunchao/CentOS 7 x86_64
/dev/mapper/vgqjc-lvqjc   8125880   36852   7653216   1% /mnt
[root@localhost ~]# 
```


下面我们将对LVM进行扩容和缩减操作，所以向/mnt中写入测试数据以验证LVM 的磁盘动态管理。

```
touch /mnt/qjc
```

二、LVM的在线扩展  

    分为两种情况：
    第一种情况，需要扩展的pe数量，vg可以提供

```
[root@localhost ~]# lvs
  LV     VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvqjc  vgqjc  -wi-ao----   8.00g                                                   
  lvtest vgtest -wi-ao---- 160.00m                                                    
[root@localhost ~]# lvextend -L +100M /dev/vgtest/lvtest 
  Size of logical volume vgtest/lvtest changed from 160.00 MiB (40 extents) to 260.00 MiB (65 extents).
  Logical volume vgtest/lvtest successfully resized.
[root@localhost ~]# lvs
  LV     VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvqjc  vgqjc  -wi-ao----   8.00g                                                   
  lvtest vgtest -wi-ao---- 260.00m                                                   
[root@localhost ~]# 
```


查看实际的磁盘容量，发现并没改变，需要对文件系统进行扩容

```
 resize2fs /dev/vgtest/lv/test

[root@localhost ~]# df -Th
Filesystem                Type      Size  Used Avail Use% Mounted on
/dev/sda5                 xfs        20G  3.9G   16G  20% /
devtmpfs                  devtmpfs  472M     0  472M   0% /dev
tmpfs                     tmpfs     488M     0  488M   0% /dev/shm
tmpfs                     tmpfs     488M  8.7M  479M   2% /run
tmpfs                     tmpfs     488M     0  488M   0% /sys/fs/cgroup
/dev/sda2                 xfs        10G  114M  9.9G   2% /app
/dev/sda1                 xfs       197M  133M   65M  68% /boot
tmpfs                     tmpfs      98M  4.0K   98M   1% /run/user/42
tmpfs                     tmpfs      98M   32K   98M   1% /run/user/1000
/dev/sr0                  iso9660   8.8G  8.8G     0 100% /run/media/qijunchao/CentOS 7 x86_64
/dev/mapper/vgtest-lvtest ext4      151M  1.6M  139M   2% /mnt
[root@localhost ~]# resize2fs /dev/vgtest/lvtest 
resize2fs 1.42.9 (28-Dec-2013)
Filesystem at /dev/vgtest/lvtest is mounted on /mnt; on-line resizing required
old_desc_blocks = 2, new_desc_blocks = 3
The filesystem on /dev/vgtest/lvtest is now 266240 blocks long.

[root@localhost ~]# df -Th
Filesystem                Type      Size  Used Avail Use% Mounted on
/dev/sda5                 xfs        20G  3.9G   16G  20% /
devtmpfs                  devtmpfs  472M     0  472M   0% /dev
tmpfs                     tmpfs     488M     0  488M   0% /dev/shm
tmpfs                     tmpfs     488M  8.7M  479M   2% /run
tmpfs                     tmpfs     488M     0  488M   0% /sys/fs/cgroup
/dev/sda2                 xfs        10G  114M  9.9G   2% /app
/dev/sda1                 xfs       197M  133M   65M  68% /boot
tmpfs                     tmpfs      98M  4.0K   98M   1% /run/user/42
tmpfs                     tmpfs      98M   32K   98M   1% /run/user/1000
/dev/sr0                  iso9660   8.8G  8.8G     0 100% /run/media/qijunchao/CentOS 7 x86_64
/dev/mapper/vgtest-lvtest ext4      248M  2.1M  231M   1% /mnt
[root@localhost ~]# 
resize2fs /dev/vgtest/lvtest　　＃更新文件系统的大小，即激活

resize2fs -f　/dev/vgtest/lvtest　500M　＃强制设置大小

dump2fs /dev/vgtest/lvtest　　＃查看ext系列文件系统
```

第二种情况，vg空间不够，需先扩展vg，扩展vg就是往vg中加pv

新建pv, 进行扩容，再查看

```
[root@localhost ~]# pvcreate /dev/sdg
  Physical volume "/dev/sdg" successfully created.
[root@localhost ~]# vgextend vgtest /dev/sdg
  Volume group "vgtest" successfully extended
[root@localhost ~]# vgs
  VG     #PV #LV #SN Attr   VSize  VFree 
  vgqjc    1   1   0 wz--n- 19.98g 11.98g
  vgtest   2   1   0 wz--n-  1.99g <1.74g
[root@localhost ~]# 
[root@localhost ~]# lvextend -L 1G /dev/vgtest/lvtest 
  Size of logical volume vgtest/lvtest changed from 260.00 MiB (65 extents) to 1.00 GiB (256 extents).
  Logical volume vgtest/lvtest successfully resized.
[root@localhost ~]# lvs
  LV     VG     Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvqjc  vgqjc  -wi-ao---- 8.00g                                                    
  lvtest vgtest -wi-ao---- 1.00g   
```

查看测试数据：

```
[root@localhost ~]# ll /mnt
total 16
drwx------. 2 root root 16384 Aug 11 22:25 lost+found
-rw-r--r--. 1 root root     0 Aug 11 23:33 qjc
[root@localhost ~]# 
```


3：LVM的缩减操作：

1：umount 文件系统

2：缩减文件系统

```
[root@localhost ~]# lvs
  LV     VG     Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvqjc  vgqjc  -wi-a----- 8.00g                                                    
  lvtest vgtest -wi-a----- 1.00g                                                    
[root@localhost ~]# resize2fs /dev/vgqjc/lvqjc 5G
resize2fs 1.42.9 (28-Dec-2013)
Resizing the filesystem on /dev/vgqjc/lvqjc to 1310720 (4k) blocks.
The filesystem on /dev/vgqjc/lvqjc is now 1310720 blocks long.

[root@localhost ~]# lvreduce -L 5G /dev/vgqjc/lvqjc 
  WARNING: Reducing active logical volume to 5.00 GiB.
  THIS MAY DESTROY YOUR DATA (filesystem etc.)
Do you really want to reduce vgqjc/lvqjc? [y/n]: y
  Size of logical volume vgqjc/lvqjc changed from 8.00 GiB (512 extents) to 5.00 GiB (320 extents).
  Logical volume vgqjc/lvqjc successfully resized.
[root@localhost ~]# lvs
  LV     VG     Attr       LSize Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lvqjc  vgqjc  -wi-a----- 5.00g                                                    
  lvtest vgtest -wi-a----- 1.00g                                                    
[root@localhost ~]# ls #检查文件是否丢失
```


挂载查看

```
[root@localhost ~]# mount /dev/vgqjc/lvqjc /mnt
[root@localhost ~]# df -Th
Filesystem              Type      Size  Used Avail Use% Mounted on
/dev/sda5               xfs        20G  3.9G   16G  20% /
devtmpfs                devtmpfs  472M     0  472M   0% /dev
tmpfs                   tmpfs     488M     0  488M   0% /dev/shm
tmpfs                   tmpfs     488M  8.7M  479M   2% /run
tmpfs                   tmpfs     488M     0  488M   0% /sys/fs/cgroup
/dev/sda2               xfs        10G  114M  9.9G   2% /app
/dev/sda1               xfs       197M  133M   65M  68% /boot
tmpfs                   tmpfs      98M  4.0K   98M   1% /run/user/42
tmpfs                   tmpfs      98M   32K   98M   1% /run/user/1000
/dev/sr0                iso9660   8.8G  8.8G     0 100% /run/media/qijunchao/CentOS 7 x86_64
/dev/mapper/vgqjc-lvqjc ext4      4.8G   32M  4.5G   1% /mnt
[root@localhost ~]# 
```


查看测试文件：

```
[root@localhost ~]# ll /mnt
total 16
drwx------. 2 root root 16384 Aug 11 22:25 lost+found
-rw-r--r--. 1 root root     0 Aug 11 23:33 qjc
[root@localhost ~]# 
```


测试成功。

删除LVM

如果要彻底的来移除LVM的话，需要把创建的步骤反过来操作。

1：卸载 文件系统

2：删除lv

3:删除vg

4:删除pv

```
[root@localhost ~]# lvremove /dev/mapper/vgqjc-lvqjc 
Do you really want to remove active logical volume vgqjc/lvqjc? [y/n]: y
  Logical volume "lvqjc" successfully removed
[root@localhost ~]# vgremove /dev/mapper/vgqjc
  Volume group "vgqjc" successfully removed
[root@localhost ~]# pvremove /dev/sdl
  Labels on physical volume "/dev/sdl" successfully wiped.
[root@localhost ~]# 
```


LVM删除成功