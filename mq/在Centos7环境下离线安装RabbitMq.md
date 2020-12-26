# 在Centos7环境下离线安装RabbitMq

> 操作系统：Centos7
>
> RabbitMQ 3.7.8

## 一、准备工作

需要准备rpm包：

- [erlang-21.3.8.2 ](https://github.com/rabbitmq/erlang-rpm/releases/download/v21.3.8.2/erlang-21.3.8.2-1.el7.x86_64.rpm)
- [socat-1.7.3.2-2.el7.x86_64.rpm](https://centos.pkgs.org/7/centos-x86_64/socat-1.7.3.2-2.el7.x86_64.rpm.html)
- [RabbitMQ 3.7.8](https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.7.8/rabbitmq-server-3.7.8-1.el7.noarch.rpm)

### 1、erlang下载

erlang的版本会影响到rabbitmq的安装，两者有个版本对照

查看对照的地址：http://www.rabbitmq.com/which-erlang.html

下载地址：http://www.erlang.org/downloads/21

### 2、rabbitmq下载

rabbitmq下载官网地址：http://www.rabbitmq.com/

官网没有找到历史版本下载地址，使用github下载地址下载

## 二、开始安装
### 1、rabbitmq是用erlang写的，所以需要先装erlang。

安装erlang：

```shell
yum install -y ./erlang-21.3.8.2-1.el7.x86_64.rpm
```

测试是否安装成功：

```shell
erl -v
```

### 2、安装rabbitmq：

```shell
yum install -y ./rabbitmq-server-3.7.8-1.el7.noarch.rpm
```

出现类似：socat-1.7.3.2-2.el7.x86_64: [Errno 256] No more mirrors to try 的错误

这时需要装一个socat：

```shell
yum install -y ./socat-1.7.3.2-2.el7.x86_64.rpm
```

然后在装rabbitmq就可以了

### 3、启动mq

```shell
rabbitmq-server start   前台启动mq

rabbitmq-server -detached   后台启动mq
```

### 4、启用webUI插件：执行命令 

```shell
rabbitmq-plugins enable rabbitmq_management
```

### 5、浏览器访问

可以在本机浏览器输入 http://localhost:15672 访问rabbitmq主页，账户 guest 密码 guest ，此处这个默认账号，只能本机登录，这时可以创建一个用户，其他人可以通过这个用户访问。

### 6、创建用户：

```shell
rabbitmqctl add_user admin admin123
```

​      (这里的admin是用户名，admin123是密码 ，可自行修改)

### 7、设置角色：

```shell
rabbitmqctl set_user_tags admin administrator
```

### 8、设置默认vhost（“/”）访问权限     

```shell
rabbitmqctl set_permissions -p “/” admin “.” “.” “.*”
```

### 9、查看admin账户的权限

执行命令 

```shell
rabbitmqctl list_user_permissions admin
```

此时可以在登录http://localhost:15672  ，这里的localhost可以替换未安装位置的id，用户使用admin，如果启动没问题，但是访问不通，这时应该看一下防火墙，

```shell
systemctl status firewalld.service         查看防火墙状态

firewall-cmd --zone=public --add-port=15672/tcp --permanent  开15672端口

firewall-cmd --zone=public --add-port=5672/tcp --permanent  开5672端口
```

参数：permanent   表示永久生效，没有此参数重启后失效

或者把防火墙关掉：

```shell
sudo systemctl stop firewalld.service          关闭运行的防火墙
```

默认端口：
web管理插件端口：15672
程序访问接口：5672

## 三、设置RabbitMq开机自启
使用yum安装的，可以直接输入命令：

```shell
chkconfig rabbitmq-server on
```

## 四、rabbitmq常用命令
### 应用管理

```shell
$ rabbitmqctl status //显示RabbitMQ中间件的所有信息
$ rabbitmqctl stop //停止RabbitMQ应用，关闭节点
$ rabbitmqctl stop_app //停止RabbitMQ应用
$ rabbitmqctl start_app //启动RabbitMQ应用
$ rabbitmqctl restart //重置RabbitMQ节点
$ rabbitmqctl force_restart //强制重置RabbitMQ节点
```

### 用户管理

```shell
$ rabbitmqctl add_user username password //添加用户
$ rabbitmqctl delete_user username //删除用户
$ rabbitmqctl change_password username newpassword //修改密码
$ rabbitmqctl list_users //列出所有用户
```

### 权限控制管理

```shell
 $ rabbitmqctl add_vhost vhostpath //创建虚拟主机
 $ rabbitmqctl delete_vhost vhostpath //删除虚拟主机
 $ rabbitmqctl list_vhosts //列出所有虚拟主机
 $ rabbitmqctl set_permissions [-p vhostpath] username <conf> <write> <read> //设置用户权限
 $ rabbitmqctl clear_permissions [-p vhostpath] username //删除用户权限
 $ rabbitmqctl list_permissions [-p vhostpath] //列出虚拟机上的所有权限
 $ rabbitmqctl list_user_permissions username //列出用户权限
```

 ### 集群管理

```shell
$ rabbitmqctl cluster_status //获得集群配置信息
$ rabbitmqctl join_cluster rabbit@localhost --ram | --disc //加入到rabbit节点中，使用内存模式或者磁盘模式
$ rabbitmqctl change_cluster_node_type disc | ram //修改存储模式
$ rabbitmqctl set_cluster_name newname //修改名字
```

### 查看管理

```shell
$ rabbitmqctl list_queues [-p <vhostpath>]  //查看所有队列
$ rabbitmqctl list_exchanges [-p <vhostpath>] //查看所有交换机
$ rabbitmqctl list_bindings [-p <vhostpath>] //查看所有绑定
$ rabbitmqctl list_connections //查看所有连接
$ rabbitmqctl list_channels //查看所有信道
$ rabbitmqctl list_consumers //查看所有消费者信息
```

## 五、本地附件

- [erlang-21.3.8.2 ](./assets/erlang-21.3.8.2-1.el7.x86_64.rpm)
- [socat-1.7.3.2-2.el7.x86_64.rpm](./assets/socat-1.7.3.2-2.el7.x86_64.rpm.html)
- [RabbitMQ 3.7.8](./assets/rabbitmq-server-3.7.8-1.el7.noarch.rpm)