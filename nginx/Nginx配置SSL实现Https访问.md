# Nginx配置SSL实现Https访问

> 实现Https首先需要向管理机构申请证书，而我们此次由于是练习目的，所以通过Openssl自己生成证书。首先我们需要用到生成证书的Openssl软件。
>

## 1. 安装Openssl

　　下载地址：Win32/Win64 OpenSSL Installer for Windows - Shining Light Productions （根据系统选择32位或者64位版本下载安装）。

　　下载完成后，进行安装。

## 2.安装ActivePerl 

（此软件目的为了解析pl文件，部分系统不安装也可以实现本教程的功能，安装该软件目的为了学习perl）。

下载地址：http://www.activestate.com/activeperl/downloads/  （根据系统选择win32或者win64版本下载安装）。

**可以不安装**

## 3. 配置环境变量

在环境变量中添加环境变量

变量名： OPENSSL_HOME

变量值：（变量值为openssl安装位置）

在path变量结尾添加如下 ： %OPENSSL_HOME%;

## 4. 生成证书　　 

###  （1）创建目录 
首先在 nginx安装目录中创建ssl文件夹用于存放证书。比如我的文件目录为 H:\nginx\ssl

以管理员身份进入命令行模式，进入ssl文件夹。 命令为： cd  H:\nginx\ssl

###　　（2） 创建私钥

在命令行中执行命令： 

```
openssl genrsa -des3 -out server.key 1024     （server文件名可以自定义）
```

如下所示：

```shell
PS H:\nginx\ssl> openssl genrsa -des3 -out server.key 1024
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
```

输入密码后，再次重复输入确认密码。记住此密码，后面会用到。

### （3）创建csr证书

在命令行中执行命令：  

```
openssl req -new -key server.key -out server.csr    （key文件为刚才生成的文件，server为自定义文件名）
```

```shell
PS H:\nginx\ssl> openssl req -new -key server.key -out server.csr
Enter pass phrase for server.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:192.168.10.161
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
PS H:\nginx\ssl>
```

如上所示，执行上述命令后，需要输入信息。输入的信息中最重要的为 Common Name，这里输入的域名即为我们要使用https访问的域名。

以上步骤完成后，ssl文件夹内出现两个文件：

```shell
server.csr
server.key
```

### （4）去除密码。

在加载SSL支持的Nginx并使用上述私钥时除去必须的口令，否则会在启动nginx的时候需要输入密码。

复制server.key并重命名为server.key.org 

可以使用此命令行，也可以使用鼠标操作   

```
copy server.key server.key.org
```

去除口令，在命令行中执行此命令：  

```
openssl rsa -in server.key.org -out server.key  （server为自定义文件名）
```

如下图所示，此命令需要输入刚才设置的密码。

```shell
PS H:\nginx\ssl> openssl rsa -in server.key.org -out server.key
Enter pass phrase for server.key.org:
writing RSA key

```

###　　　（5）生成crt证书

在命令行中执行此命令：

```
openssl x509 -req -days 36500 -in server.csr -signkey server.key -out server.crt （server为自定义文件名）
```

```shell
PS H:\nginx\ssl> openssl x509 -req -days 36500 -in server.csr -signkey server.key -out server.crt
Certificate request self-signature ok
subject=C = AU, ST = Some-State, O = Internet Widgits Pty Ltd, CN = 192.168.10.161

```

 证书生成完毕，ssl文件夹中一共生成如下4个文件，我们需要使用到的是server.crt和server.key。

```shell
server.crt
server.csr
server.key
server.key.org
```

## 5. 修改nginx.conf文件

nginx.conf文件位于：C:\wnmp\nginx\conf

找到该文件中如下代码的位置进行修改：

```
# HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;
    
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;
    
    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;
    
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;
    
    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

```
修改为：
```
# HTTPS server
    server {
        listen       443 ssl;
        server_name    www.server.com;
    
        ssl_certificate      H:\\nginx\\ssl\\server.crt;
        ssl_certificate_key  H:\\nginx\\ssl\\server.key;
    
        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;
    
        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
    
        location / {
            root   html;
            index  index.html index.htm; 
        }                 
    }
    #modify by lee 20160907 for https -s 
```
重启nginx。

在浏览器中，访问 https://IP。发现出现证书认证，并能够成功访问。（IP为生成证书时，Common Name输入的域名）

上面的https被红色划线是因为我们使用的是自己生成的证书，此证书不受浏览器信任，如果想使其变为绿色，则需要向证书管理机构进行申请。

## 6. 添加重定向，自动跳转使用https。

在nginx.conf中virtual host中如下代码位置添加一行代码：

    listen 80;
    server_name  localhost;
    rewrite ^(.*) https://$server_name$1 permanent;
重启nginx。

访问http://192.168.10.161:8090，会发现浏览器自动跳转到https://192.168.10.161，并能够成功访问。

至此，https访问配置成功完成。