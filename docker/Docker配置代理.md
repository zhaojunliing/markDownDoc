# docker从安装到拉取镜像全使用代理方法

## 一、安装

1. 设置apt-get和curl的默认代理，下载docker都需要apt-get和curl从链接中下载

- apt-get设置代理：

  - 先新建目录`mkdir /etc/apt/apt.conf.d`

  - 再写入文件.`proxy.conf`：

    ```java
    Acquire::http::proxy "http://127.0.0.1:8000/";
    Acquire::https::proxy "https://127.0.0.1:8000/";
    ```

    里面的内容改为本机ip和监测端口

- curl设置代理

  curl设置代理有的地方说可以通过设置环境变量的方式来实现，如：

  ```shell
  export http_proxy=http://127.0.0.1:8000
  ```

  但是实际测试并不行，似乎只能设置后的第一次能使用代理，这对于执行sh脚本来说并不适合。

  - 在`~`目录下新建`.curlrc`文件

    ```java
    proxy="http://192.168.0.101:10809"
    ```

## 二、下载docker

使用官方的下载脚本进行下载。

```shell
 curl -fsSL https://test.docker.com -o test-docker.sh
 sudo sh test-docker.sh
```

**如果前面未进行配置，这里下载会失败！**

下载完毕后可以使用命令查看是否下载成功

```shell
docker version
```

#### 使用docker获取ubuntu实现hello world

这里以拉取[ubuntu镜像](https://so.csdn.net/so/search?q=ubuntu镜像&spm=1001.2101.3001.7020)为例，设置docker默认代理。shell

```shell
docker run ubuntu:15.10 /bin/echo "Hello world"
```

这里如果没有配置国内镜像或者代理，会拉取不到。

- 新建`/etc/systemd/system/docker.service.d`目录

  ```shell
  mkdir /etc/systemd/system/docker.service.d
  ```

- 创建proxy.conf文件

  ```properties
  [Service]
  Environment="HTTP_PROXY=http://192.168.0.101:10809/"
  Environment="HTTPS_PROXY=http://192.168.0.101:10809/"
  ```

**`[Service]`不能删除！**

- 配置完成重启docker

  ```shell
   systemctl daemon-reload
   systemctl restart docker
  ```

- 查看有无配置成功

  ```shell
  root@nao-VMware-Virtual-Platform:/etc/systemd/system/docker.service.d# systemctl show --property=Environment docker
  Environment=HTTP_PROXY=http://192.168.0.101:10809/ HTTPS_PROXY=http://192.168.0.101:10809/
  ```

- 在使用上述命令可以获取到镜像

```shell
root@nao-VMware-Virtual-Platform:/etc/systemd/system/docker.service.d# docker run ubuntu:15.10 /bin/echo "Hello world"
Unable to find image 'ubuntu:15.10' locally
15.10: Pulling from library/ubuntu
7dcf5a444392: Pull complete 
759aa75f3cee: Pull complete 
3fa871dc8a2b: Pull complete 
224c42ae46e7: Pull complete 
Digest: sha256:02521a2d079595241c6793b2044f02eecf294034f31d6e235ac4b2b54ffc41f3
Status: Downloaded newer image for ubuntu:15.10
Hello world
```

文章知识点与官方知识档案匹配，可进一步学习相关知识