## 一.centos7.x环境

```
//启动docker服务
sudo systemctl start docker
//重启docker服务
sudo systemctl restart  docker
//关闭docker服务
sudo systemctl stop docker
//查看docker服务状态
sudo systemctl status docker
```

### 1.1 docke服务关闭后还是启动状态问题

```
//sudo systemctl stop docker命令后报：
Warning: Stopping docker.service, but it can still be activated by:
docker.socket
//sudo systemctl status docker查看docker服务状态,docker仍然是active状态
执行命令：
sudo systemctl stop docker.socket停止docker.socket服务后再停止docker
```

### 1.2 docker配置容器日志大小

```
// 1.修改/etc/docker/daemon.json 
// 添加属性“log-driver”,“log-opts”,其中max-size为日志最大大小，max-file为最大文件数,
// 修改后需要reload,后重启
//# systemctl daemon-reload  
//# systemctl restart docker
{
"log-driver": "json-file",
"log-opts": {"max-size": "5g", "max-file": "4"}
}
// 2.修改docker-compose.yml 增加节点“web-nginx”,具体节点名按nginx前端服务决定
web-nginx: 
	logging:
      	driver: "json-file"
      	options:
        		max-size: "5g"
```

### 1.2docker清除无效容器镜像

```
//数据无价，操作需谨慎
docker system prune 命令可以用于清理磁盘，删除关闭的容器、无用的数据卷和网络，以及dangling镜像(即无tag的镜像);
docker system prune -a 命令清理得更加彻底，可以将没有容器使用Docker镜像都删掉;
```

### 1.3 docker手动清除日志
#### 1.创建sh脚本 clean_docker_log.sh，内容如下

```shell
echo "======== start clean docker containers logs ========"

logs=$(find /var/lib/docker/containers/ -name *-json.log)

for log in $logs
        do
                echo "clean logs : $log"
                cat /dev/null > $log
        done

echo "======== end clean docker containers logs ========"
```

#### 2.给脚本加权限

```shell
chmod +x clean_docker_log.sh
```

#### 3.执行脚本（切到脚本所在目录）

```shell
./clean_docker_log.sh
```

## 二.centos6.x环境

```shell
//启动docker服务
sudo service docker start
//重启docker服务
sudo service docker restart
//关闭docker服务
service docker stop
//查看docker服务状态
sudo docker ps -a
```

