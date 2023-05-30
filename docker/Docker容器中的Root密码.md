# Docker容器中的Root密码

使用`-u`选项时，可以使用根用户(ID = 0)而不是提供的默认用户登录Docker容器。 E.g。

```shell
docker exec -u 0 -it mycontainer bash
```

> root(id = 0)是容器中的默认用户。 图像开发人员可以创建其他用户。 这些用户可以通过名称访问。 传递数字ID时，用户不必存在于容器中。

**更新**：当然，您也可以对容器使用Docker管理命令来运行此命令：

```shell
docker container exec -u 0 -it mycontainer bash
```

```shell
docker exec -u root -it <containerName> bash
```

