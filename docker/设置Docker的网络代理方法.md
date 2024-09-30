## 方法1：配置Docker守护进程

配置Docker守护进程以使用代理是最全局的方法之一，适用于所有通过守护进程启动的容器和构建操作。您可以通过编辑/etc/systemd/system/docker.service.d/http-proxy.conf文件来设置环境变量，为Docker守护进程指定代理：

代码语言：txt

复制

```txt
[Service]
Environment="HTTP_PROXY=http://your-proxy-address:port/"
Environment="HTTPS_PROXY=http://your-proxy-address:port/"
Environment="NO_PROXY=decs.com,localhost,127.0.0.1"
```

注意：HTTPS_PROXY既可以使用https的地址，也可以使用http的地址。

记得之后重新加载守护进程配置并重启Docker服务：

代码语言：bash

复制

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 方法2：为单个容器配置网络代理

如果您只希望为特定的Docker容器配置网络代理，可以在运行容器时通过--env参数指定：

代码语言：bash

复制

```bash
docker run -it --env HTTP_PROXY="http://your-proxy-address:port" --env HTTPS_PROXY="http://your-proxy-address:port" ubuntu bash
```

这种方法的优势在于其灵活性，允许您为不同的容器指定不同的代理配置。

## 方法3：在Dockerfile中配置代理

当您在构建自己的镜像时，可以在Dockerfile中设置环境变量，让构建过程使用代理。这对于在构建过程中需要下载外部依赖的情况非常有用：

代码语言：txt

复制

```txt
ARG HTTP_PROXY="http://your-proxy-address:port/"
ARG HTTPS_PROXY="http://your-proxy-address:port/"

ENV HTTP_PROXY=${HTTP_PROXY}
ENV HTTPS_PROXY=${HTTPS_PROXY}
```

在构建镜像时，使用--build-arg参数来确保这些设置被应用：

代码语言：bash

复制

```bash
docker build --build-arg HTTP_PROXY="http://your-proxy-address:port/" --build-arg HTTPS_PROXY="http://your-proxy-address:port/" -t your-image-name .
```

## 方法4：使用Docker Compose配置代理

Docker Compose是处理多容器Docker应用的一个工具。您可以在docker-compose.yml文件中为服务设置代理环境变量：

代码语言：yaml

复制

```yaml
version: '3'
services:
  your-service:
    image: your-image
    environment:
      - HTTP_PROXY=http://your-proxy-address:port/
      - HTTPS_PROXY=http://your-proxy-address:port/
```

这种方法简化了复杂应用的代理配置过程，只需一次性设置即可应用于所有相关服务。

## 方法5：使用systemctl edit修改Docker服务的网络代理

对于那些使用systemd管理Docker服务的系统，systemctl edit命令提供了一种直接且安全的方式来修改Docker服务的配置，尤其是对于配置网络代理这一需求。这种方法不需要直接修改Docker服务的主配置文件，避免了手动操作带来的风险。

步骤：

### 打开Docker服务的覆盖编辑器

使用下面的命令来开启Docker服务的系统编辑器：

代码语言：bash

复制

```bash
sudo systemctl edit docker.service
```

这将打开一个空的文件（如果是第一次执行此操作），等待您输入需要覆盖的配置。

如果需要更换编辑器为vim，可以：

代码语言：bash

复制

```bash
sudo EDITOR=vim systemctl edit docker.service
```

### 添加HTTP/HTTPS代理环境变量

在打开的编辑器中，您需要指定代理配置。请添加如下配置以设置HTTP和HTTPS代理：

代码语言：txt

复制

```txt
[Service]
Environment="HTTP_PROXY=http://your-proxy-address:port/"
Environment="HTTPS_PROXY="http://your-proxy-address:port/"
```

替换http://your-proxy-address:port/为您实际的代理[服务器](https://cloud.tencent.com/act/pro/promotion-cvm?from_column=20065&from=20065)地址。

### 保存并关闭编辑器

完成编辑后，保存文件并退出编辑器。systemd将自动创建一个名为/etc/systemd/system/docker.service.d/override.conf的文件，其中包含您添加的配置。

### 重新加载daemon并重启Docker服务

为了让刚才的更改生效，需要重新加载systemd daemon并重启Docker服务：

代码语言：bash

复制

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker.service
```

这个方法的优点是，通过使用systemctl edit命令，您可以避免直接编辑Docker的主配置文件，而是通过创建一个特定的覆盖文件来实现您的配置。这样做的好处是，原始配置文件保持不变，方便将来的升级和维护，并且您的自定义配置也不会在软件包更新时被覆盖。