##  Linux如何查看端口被哪个进程占用

1、lsof -i:端口号

2、netstat -tunlp|grep 端口号

都可以查看指定端口被哪个进程占用的情况

**【步骤一】lsof -i**

lsof -i 用以显示符合条件的进程情况，lsof(list open files)是一个列出当前系统打开文件的工具。**以root用户来执行lsof -i命令**，如下图

![img](F:\github\markDownDoc\linux\assets\548763-20181130100134318-1645568545.png)

**【步骤二】lsof -i:端口号**

lsof -i:端口号，用于查看某一端口的占用情况，比如查看22号端口使用情况，lsof -i:22，如下图

![img](F:\github\markDownDoc\linux\assets\548763-20181130100213743-584866148.png)

**【步骤三】netstat -tunlp**

netstat -tunlp用于显示tcp，udp的端口和进程等相关情况，如下图

![img](F:\github\markDownDoc\linux\assets\548763-20181130100235459-1900854807-1566866198165.png)

**【步骤四】netstat -tunlp|grep 端口号**

netstat -tunlp|grep 端口号，用于查看指定端口号的进程情况，如查看22端口的情况，netstat -tunlp|grep 22，如下图

![img](F:\github\markDownDoc\linux\assets\548763-20181130100417199-325925913.png)