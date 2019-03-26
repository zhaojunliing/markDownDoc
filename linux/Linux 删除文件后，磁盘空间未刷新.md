# Linux 删除文件后，磁盘空间未刷新
删除了一个大文件，20G以上；
重新df -hl查询磁盘空间，没有变化，可用空间仍然很小；
百度了一下，了解到：通过rm或者文件管理器删除文件将会从文件系统的文件夹结构上解除链接(unlink).然而假设文件是被打开的（有一个进程正在使用），那么进程将仍然能够读取该文件，磁盘空间也一直被占用。
所以需要找到已删除却仍在被占用的进程，然后kill掉：

![](https://raw.githubusercontent.com/zhaojunliing/markDownDoc/master/youDaoYun/Linux/2019-03-26_170408.png)

可以看到进程号为3928，7051的进程未真正删除，然后kill 3928，kill 7051即可
再次输入lsof | grep deleted查询为空，即没有被占用的删除的进程了；

最后dh -hl查询磁盘空间，正常了。