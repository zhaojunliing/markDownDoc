# Windows安装mysql8(免安装版)

1.下载mysql8 zip免安装包

[MySQL :: Begin Your Download](https://dev.mysql.com/downloads/file/?id=511178)

2.在任意非中文目录下解压

3、配置环境变量

4、以**管理员身份**打开cmd，执行安装命令

（1）初始化mysql服务,并记录生成的用户密码root的随机密码

```shell
mysqld --initialize --user=mysql --console
```

（2）安装mysql服务

```shell
mysqld --install
```

3.启动mysql服务

```shell
net start mysql
```

4.修改mysql密码

使用默认生成的密码进行登录（默认密码:=il1Ouify;qt）

```shell
mysql -u root -p
```

修改密码（使用原生密码策略）

```shell
alter user 'root'@'localhost' inentified with mysql_native_password by 'root';
flush privileges;
```

