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

