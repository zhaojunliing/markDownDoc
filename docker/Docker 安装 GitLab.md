## 安装

环境Centos 7 X64

### 1.拉取镜像文件

```shell
docker pull gitlab/gitlab-ce:latest
```

### 2.docker 安装

```shell
docker run -d --hostname 192.168.10.159 --publish 543:443 --publish 81:80 --publish 222:22 --name gitlab --restart always --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab     gitlab/gitlab-ce
```

浏览器访问: http://192.168.10.159:81  默认用户名: root 密码: 5iveL!fe

### 3、配置完成后重启docker服务

```shell
docker stop gitlab

docker start gitlab
```

### 4、中文设置

切换到官方镜像之后， 中文设置方法： 依次点击工具栏最右侧用户头像 》 `Settings` 》 `Preferred language` ， 然后选择 `简体中文` 即可。

## 升级

> 查看版本号命令：cat /opt/gitlab/embedded/service/gitlab-rails/VERSION

### 1、小版本升级

（例如从 8.8.2 升级到 8.8.3）， 参照官方的说明， 将原来的容器停止， 然后删除：
```shell
docker stop gitlab
docker rm gitlab
```

​	然后重新拉一个新版本的镜像下来：

```shell
docker pull gitlab/gitlab-ce:latest
```

  还使用原来的运行命令运行：

```shell
docker run -d --hostname 192.168.10.159 --publish 543:443 --publish 81:80 --publish 222:22 --name gitlab --restart always --volume /srv/gitlab/config:/etc/gitlab --volume /srv/gitlab/logs:/var/log/gitlab --volume /srv/gitlab/data:/var/opt/gitlab     gitlab/gitlab-ce
```

### 2、大版本升级
​	请按照gitlab官网提供的版本升级版本进行升级。

https://docs.gitlab.com/ee/policy/maintenance.html#upgrade-recommendations