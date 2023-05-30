# Centos 7安装MySQL（5.6和5.7超详细版）
## 1、MySQL安装

下载mysql源安装包：

  ```
  wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
  ```

安装mysql源：

  ```
  yum localinstall mysql57-community-release-el7-8.noarch.rpm
  ```

检查mysql源是否安装成功：

  ```
  yum repolist enabled | grep "mysql.*-community.*"
  ```

修改yum源 【可跳过】：

  ```
  vim /etc/yum.repos.d/mysql-community.repo
  ```

改变默认安装的mysql版本。比如要安装5.6版本，将5.7源的enabled=1改成enabled=0。然后再将5.6源的enabled=0改成enabled=1即可。
备注：enabled=1表示即将要安装的mysql版本，这个文件也可以不修改，默认安装mysql最高版本


这一步才是真正安装mysql：

```shell
yum install mysql-community-server
```

启动MySQL服务并设置开机启动

```bash
#启动服务
systemctl start mysqld
```

```bash
#设置开机自启
systemctl enable mysqld
systemctl daemon-reload
```

修改root本地登录密码
1）查看mysql密码

```bash
grep 'temporary password' /var/log/mysqld.log
```

2）连接mysql，密码为上图中的红色框出来的部分

```bash
mysql -uroot -p
```

3）修改密码【注意：后面的分号一定要跟上】

```mysql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

或者：

```mysql
mysql> set password for 'root'@'localhost'=password('MyNewPass4!');
```

如果密码太简单报错，进行如下设置：

```mysql
mysql> set global validate_password_length=4 ;
mysql> set global validate_password_policy=0;
```

修改完密码之后退出重新登陆

添加远程登录用户

```mysql
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'pwd' WITH GRANT OPTION;
‘root’为用户名，'pwd’为密码
```

