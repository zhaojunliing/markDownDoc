# Ubuntu中允许root远程访问

客户端通过ssh远程连接到ubuntu服务器

1、首先，确保服务器在安装过程中，选择安装openssh软件。

如没有进行安装，请使用以下命令进行安装：

```shell
apt-get install -y openssh-server
```

2、安装完成后，使用root登录系统：

```she
su - root
```

3、编辑vim /etc/ssh/sshd_config文件，将PermitRootLogin 的值改为yes

```shell
# Authentication:
LoginGraceTime 120
PermitRootLogin yes
StrictModes yes
```

4、因为为root账户设置了密码，所以还要更改PermitEmptyPasswords为 no。然后：wq保存文件。

```shell
# To enable empty passwords, change to yes (NOT RECOMMENDED)
PermitEmptyPasswords no
```

5、然后重启ssh服务，如下：

```she
service ssh resatrt
```

6、完成，可以正常使用root用户进行访问了。