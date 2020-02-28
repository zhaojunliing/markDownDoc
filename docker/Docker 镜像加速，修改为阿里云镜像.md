# Docker 镜像加速，修改为阿里云镜像

> 首先访问 登录阿里云 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors，会获取专属的镜像地址
>

## 1、Centos系统

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://sinp4ykv.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 2、Ubantu系统

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://sinp4ykv.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 3、其他系统

Windows系统和Mac系统参考阿里云的官网，有相关的教程。