# Docker启用2375端口访问

编辑文件 /lib/systemd/system/docker.service

修改以 ExecStart 开头的行，如下所示：

```py
ExecStart=/usr/bin/docker daemon -H fd:// -H tcp://0.0.0.0:2375
```

我添加的是“-H tcp://0.0.0.0:2375”部分。保存修改后的文件。重启 Docker 服务：

```py
sudo service docker restart
```

测试 Docker API 是否确实可以访问：

```py
curl http://localhost:2375/version
```