今天运行程序，出现了/lib64/libc.so.6: version `GLIBC_2.14’ not found问题，解决方法如下：
系统环境： redhatt6.7
[root@localhost lib64]#cd /lib64/
[root@localhost lib64]# strings /lib64/libc.so.6 |grep GLIBC
GLIBC_2.2.5
GLIBC_2.2.6
GLIBC_2.3
GLIBC_2.3.2
GLIBC_2.3.3
GLIBC_2.3.4
GLIBC_2.4
GLIBC_2.5
GLIBC_2.6
GLIBC_2.7
GLIBC_2.8
GLIBC_2.9
GLIBC_2.10
GLIBC_2.11
GLIBC_2.12
GLIBC_PRIVATE
#当前环境没有GLIBC_2.14

将rpm包放到/opt/
执行：rpm -Uvh *.rpm --nodeps --force
完美解决！



付rpm包下载链接：
链接：https://pan.baidu.com/s/1xPdqzKpcEYB-j7vkkEnziQ
提取码：8vyc