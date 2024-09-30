# docker 日志清理

1.使用排查docker使用空间

```
docker system df 
```

2.清理系统缓存

```
sudo apt autoremove
rm -rf ~/.cache/thumbnails/*
sudo rm -rf ~/.local/share/Trash/*
```

3.查找大文件夹

```
sudo du -hsx /* | sort -rh | head -10
```

更改docker日志全局设置
在主机修改docker配置问题，只对新建docker容器有效。

```shell 
# 排查Docker容器日志输出情况  docker  inspect  --format {{.LogPath}}  容器名/容器ID   
vim /etc/docker/daemon.json
{
   "registry-mirrors": [
       "https://mirror.ccs.tencentyun.com",
        "https://10odyesl.mirror.aliyuncs.com"
  ],
  "log-driver":"json-file",
  "log-opts": {"max-size":"300m", "max-file":"3"}
}

systemctl restart docker
systemctl daemon-reload 

```

更改容器配置（docker-compose）
修改 docker-compose.yml，增加如下配置。yml分级按两个空格缩进，可以使用在线yml格式校验

    logging:
      driver: "json-file"
      options:
        max-size: "50m"
        max-file: "2
查看配置：docker inspect -f ‘{{.HostConfig.LogConfig}}’ 容器名xxx