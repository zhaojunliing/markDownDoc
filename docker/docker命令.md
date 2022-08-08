通过docker cp把这个文件拷贝出来

```
docker cp xxxx:/etc/mysql/my.cnf /home/tom/
```

再拷贝回去

```
docker cp home/tom/my.cnf xxxx:/etc/mysql/
```

```
docker update --restart=always  xxxxx
```

```shell
docker logs -f xxxxx
```

```shell
docker start xxxxx
docker restart xxxxx
docker stop xxxxx
docker rm xxxxx
docker images
docker rmi xxxxx
docker ps -a
docker ps
```

```shell
--将容器转换为镜像
docker commit containerid zjl/masr:v1

--将镜像打包成文件
docker save -o masr_v1.tar zjl/masr:v1

--将文件导入到镜像中
docker load --input masr_v1.tar

```

```shell

docker build .

-f，--file 指定 dockerfile 路径
docker build -f /path/to/a/Dockerfile .

-t，--tag  指定构建的镜像名和 tag
docker build -t ubuntu-nginx:v1 . 

--构建的镜像指定多个 tag
docker build -t shykes/myapp:1.0.2 -t shykes/myapp:latest .

--add-host
可以使用一个或多个 --add-host 标志将其他主机添加到容器的 /etc/hosts 文件中

docker build --add-host=docker:10.180.0.1 .


```

