# 修改Docker工作目录	

修改 Docker 的工作路径（也就是 Docker 存储数据的路径，例如 /var/lib/docker），解决系统盘占用问题，可以通过修改 Docker 的配置文件，指定新的存储位置。以下是步骤：

## 1.停止 Docker 服务

为了避免在移动数据时 Docker 正在使用这些数据，最好先停止 Docker 服务。

```bash
sudo systemctl stop docker
```

## 2.复制数据

如果希望将当前的 Docker 数据从旧路径 /var/lib/docker/ 移动到新路径/disk/docker/lib/，可以使用 **rsync** 命令进行数据迁移。

```bash
rsync -avz /var/lib/docker/ /disk/docker/lib/
```

## 3.修改 Docker 配置

Docker 配置文件通常位于 /etc/docker/daemon.json，如果没有这个文件，可以创建一个。
编辑 /etc/docker/daemon.json 文件

```bash
sudo nano /etc/docker/daemon.json
```

修改 data-root 配置项，指向新的目录，在文件中添加如下内容：

```bash
{
  "data-root": "/disk/docker/lib"
}
```
## 4.验证配置

可以使用 docker info 命令来验证 Docker 是否成功使用新的数据目录：

```bash
docker info
```
## 5.启动 Docker 服务

```bash
sudo systemctl daemon-reload
sudo systemctl start docker
```

## 6.  删除数据
如果一切正常并且新的目录配置已生效，可以删除旧的 /var/lib/docker 目录来释放磁盘空间：

```bash
sudo rm -rf /var/lib/docker
```