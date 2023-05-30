# Zabbix 容器安装

该示例示范了如何运行 MySQL 数据库支持的 Zabbix Server 、基于 Nginx Web 服务器的 Zabbix Web 界面和 Zabbix Java 网关。

## 1. 创建专用于 Zabbix 组件容器的网络：

```shell
# docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 zabbix-net
```

## 2. 启动空的 MySQL 服务器实例：

```shell
#下载镜像
docker pull mysql:8.0
#运行镜像
docker run --net=host --name mysql-server -t \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -d mysql:8.0 \
      --character-set-server=utf8 --collation-server=utf8_bin \
      --default-authentication-plugin=mysql_native_password
```

官网提供的带有--restart unless-stopped，但是不支持，需要去掉该命令，网络使用本机网络

## 3. 启动 Zabbix Java 网关实例：

```shell
#下载镜像
docker pull zabbix/zabbix-java-gateway:alpine-6.4-latest
#运行镜像
docker run --name zabbix-java-gateway -t \
      --network=zabbix-net \
      --restart unless-stopped \
      -d zabbix/zabbix-java-gateway:alpine-6.4-latest
```

## 4. 启动 Zabbix server 实例，并将其关联到已创建的 MySQL server 实例：

```shell
#下载镜像
docker pull zabbix/zabbix-server-mysql:centos-6.4.2
#运行镜像
docker run --name zabbix-server-mysql -t \
      -e DB_SERVER_HOST="192.168.20.111" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -e ZBX_JAVAGATEWAY="zabbix-java-gateway" \
      --network=zabbix-net \
      -p 10051:10051 \
      --restart unless-stopped \
      -d zabbix/zabbix-server-mysql:centos-6.4.2
```

Zabbix server 实例将 10051/TCP 端口（Zabbix trapper）暴露给主机。

## 5. 启动 Zabbix Web 界面，并将其关联到已创建的 MySQL server 和 Zabbix server 实例：

```shell
docker pull zabbix/zabbix-web-nginx-mysql:alpine-6.4-latest
#运行镜像
docker run --name zabbix-web-nginx-mysql -t \
      -v /etc/hosts:/etc/hosts \
      -e ZBX_SERVER_HOST="192.168.20.111" \
      -e DB_SERVER_HOST="192.168.20.111" \
      -e MYSQL_DATABASE="zabbix" \
      -e MYSQL_USER="zabbix" \
      -e MYSQL_PASSWORD="zabbix_pwd" \
      -e MYSQL_ROOT_PASSWORD="root_pwd" \
      -p 83:8080 \
      --restart unless-stopped \
      -d zabbix/zabbix-web-nginx-mysql:alpine-6.4-latest
```

Zabbix web 界面实例将 80/TCP 端口（HTTP）暴露给主机。