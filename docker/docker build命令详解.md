docker build 命令原理
docker build 命令从 Dockerfile 和上下文构建镜像
构建的上下文：位于指定 PATH 或 URL 中的一组文件
构建过程可以引用上下文中的任何文件，例如，构建可以使用 COPY 指令来引用上下文中的文件
PATH：就是本地文件系统上的一个目录路径
URL：Git 地址
个人理解：以设置的上下文为根目录，在 dockerfile 中写的文件路径都会以这个上下文开始找
构建上下文是递归处理的
PATH 包含任何子目录，URL 包含 repository 及其子模块

简单的栗子
上下文为当前目录
也是最简单的 docker build 使用方式

docker build .

指定 PATH 为  .  ，因此本地目录中的所有文件都会被延迟并发送到 Docker 守护程序
PATH 指定在哪里可以找到 Docker 守护程序上构建的“上下文”的文件
请记住，守护进程可以在远程机器上运行，并且不会在客户端(运行 docker build 的地方)解析 Dockerfile
这意味着 PATH 中的所有文件都会被发送，而不仅仅是 Dockerfile 中列出的 ADD 文件
当看到 Sending build context 消息时，docker 客户端的意思是将上下文从本地机器传输到 Docker 守护进程。
构建由 Docker 守护程序(Daemon)运行
而不是 CLI(命令行)运行
构建过程做的第一件事是将整个上下文(递归)发送到守护进程
官方建议：将一个空目录作为上下文起点，并将 Dockerfile 保存在该目录中，仅添加构建 Dockerfile 所需的文件
特别注意
不要使用根目录  /  作为构建上下文的 PATH，因为会导致构建时，将硬盘驱动器的全部内容发送到 Docker 守护程序

逐一运行
Docker 守护进程将逐一运行 Dockerfile 中的指令，如有必要，会将每条指令的结果提交到新镜像，最后会输出一个最新镜像的 ID
Docker 守护进程将自动清理发送的上下文
重点：每条指令都是独立运行的，并会创建一个新镜像，因此像 RUN cd /tmp 不会对下一条自定产生任何影响
只要有可能，Docker 就会使用构建缓存来加速 Docker 构建过程，这由控制台输出中的 CACHED 消息指示
 > docker build -t svendowideit/ambassador .
 >  [internal] load build definition from Dockerfile                       0.1s
 >  => transferring dockerfile: 286B                                       0.0s
 >  [internal] load .dockerignore                                          0.1s
 >  => transferring context: 2B                                            0.0s
 >  [internal] load metadata for docker.io/library/alpine:3.2              0.4s
 >  CACHED [1/2] FROM docker.io/library/alpine:3.2@sha256:e9a2035f9d0d7ce  0.0s
 >  CACHED [2/2] RUN apk add --no-cache socat                              0.0s
 >  exporting to image                                                     0.0s
 >  => exporting layers                                                    0.0s
 >  => writing image sha256:1affb80ca37018ac12067fa2af38cc5bcc2a8f09963de  0.0s
 >  => naming to docker.io/svendowideit/ambassador                         0.0s

命令行参数
-f，--file
指定 dockerfile 路径

docker build -f /path/to/a/Dockerfile .

不指定的话，默认会读取上下文路径(  .  )下的 dockerfile

-t，--tag
指定构建的镜像名和 tag

docker build -t ubuntu-nginx:v1 . 

构建的镜像指定多个 tag

docker build -t shykes/myapp:1.0.2 -t shykes/myapp:latest .

--add-host
可以使用一个或多个 --add-host 标志将其他主机添加到容器的 /etc/hosts 文件中

docker build --add-host=docker:10.180.0.1 .

--no-cache
构建镜像时不使用缓存

--network
在构建过程中为 RUN 指令设置网络模式

更多参数可以看官方文档

https://docs.docker.com/engine/reference/commandline/build/
————————————————
版权声明：本文为CSDN博主「小菠萝测试笔记」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_33801641/article/details/120945037