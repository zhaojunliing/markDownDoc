#Linux系统上实现定时重启Tomcat服务
## 一、创建Shell脚本
新建一个tomcat.sh文件，输入以下内容：

```shell
#!/bin/bash
. /etc/profile
tomcatPath="/usr/local/tomcat"
binPath="$tomcatPath/bin"
echo "[info][$(date +'%F %H:%M:%S')]正在监控tomcat，路径：$tomcatPath"
pid=`ps -ef | grep tomcat | grep -w $tomcatPath | grep -v 'grep' | awk '{print $2}'`
if [ -n "$pid" ]; then
echo "[info][$(date +'%F %H:%M:%S')]tomcat进程为：$pid"
echo "[info][$(date +'%F %H:%M:%S')]tomcat已经启动，准备使用shutdown命令关闭..."
$binPath"/shutdown.sh"
sleep 2
pid=`ps -ef | grep tomcat | grep -w $tomcatPath | grep -v 'grep' | awk '{print $2}'`
if [ -n "$pid" ]; then
echo "[info][$(date +'%F %H:%M:%S')]使用shutdown命令关闭失败，准备kill进程..."
kill -9 $pid
echo "[info][$(date +'%F %H:%M:%S')]kill进程完毕！"
sleep 1
else
echo "[info][$(date +'%F %H:%M:%S')]使用shutdown命令关闭成功！"
fi
else
echo "[info][$(date +'%F %H:%M:%S')]tomcat未启动！"
fi
echo "[info][$(date +'%F %H:%M:%S')]准备启动tomcat..."
$binPath"/startup.sh"
```
脚本的逻辑比较简单，先查看tomcat进程是否存在，如果不存在则直接调用startup.sh命令启动tomcat，如果tomcat的进程存在则调用shutdown.sh命令停掉tomcat服务，有时会关闭失败，所以在检查一下tomcat进程时候还存在，如果存在就使用kill命令杀掉该进程，然后启动tomcat。
这里有一个地方需要注意，第二行代码（. /etc/profile）不能省掉，需要将环境变量引入到当前脚本环境中，要不然会导致启动定时任务时运行脚本失败，会报以下错误：
```
Neither the JAVA_HOME nor the JRE_HOME environment variable is defined At least one of these environment variable is needed to run this program
```
## 二、修改文件权限
要让脚本可以执行，当然要把文件设置为可执行文件了。
```shell
chmod 777 tomcat.sh
```
## 三、建立定时任务
创建相应的定时任务进行执行即可。

----------

备注摘自：https://blog.csdn.net/anya/article/details/6407280/

