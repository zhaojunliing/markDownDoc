# 一、生成根证书
### 1、配置root.conf
```ini
[ CA_default ]
copy_extensions = copy

[ req ]
default_bits       = 4096
distinguished_name = req_distinguished_name

[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
countryName_default         = CN
stateOrProvinceName         = State or Province Name (full name)
stateOrProvinceName_default = ZhengZhou
localityName                = Locality Name (eg, city)
localityName_default        = ZhengZhou
organizationName            = Organization Name (eg, company)
organizationName_default    = fhgroup
commonName                  = Common Name (e.g. server FQDN or YOUR name)
commonName_max              = 64
commonName_default          = FHGROUP CA Center
```
### 2、生成ca秘钥，得到root.key
```shell
openssl genrsa -out root.key 4096
```

### 3、生成ca证书签发请求，得到root.csr
```shell
openssl req -new -subj "/C=CN/ST=ZhengZhou/L=ZhengZhou/O=fhgroup/CN=FHGROUP CA Center" -sha256 -out root.csr -key root.key -config root.conf
```

### 4、生成ca根证书，得到root.crt
```shell
openssl x509 -req -days 36500 -in root.csr -signkey root.key -out root.crt
```

# 二、生成服务端证书
### 1、配置server.conf
```ini
[ CA_default ]
copy_extensions = copy

[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext
prompt             = no

[ req_distinguished_name ]
countryName                 = CN
stateOrProvinceName         = HeNan
localityName                = Zhengzhou
organizationName            = fhgroup
organizationalUnitName      = fh
commonName                  = www.fhgroup.cn

[ req_ext ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names

[ alt_names ]
DNS.1 = eaccess.zztlj.com
DNS.2 = *.zztlj.com
IP.1    = 192.168.10.72
```
### 2、生成秘钥，得到server.key
```shell
openssl genrsa -out server.key 2048
```

### 3、生成证书签发请求，得到server.csr
```shell
openssl req -new -sha256 -out server.csr -key server.key -config server.conf  -reqexts req_ext
```

### 4、用CA root证书生成终端用户证书，得到server.crt
```shell
openssl x509 -req -days 36500 -CA root.crt -CAkey root.key -CAcreateserial -in server.csr -out server.crt -extensions req_ext -extfile server.conf
```

# 三、nginx配置证书
修改nginx.conf
```nginx configuration pro
 server {
       listen       443 ssl;
       server_name  192.168.10.72;

       ssl_certificate H:\project\Gitee\RuoYi-Vue\key\server.crt;
       ssl_certificate_key H:\project\Gitee\RuoYi-Vue\key\server.key;

       ssl_session_cache    shared:SSL:10m;
       ssl_session_timeout  5m;

       ssl_ciphers  HIGH:!aNULL:!MD5;
       ssl_prefer_server_ciphers  on;



       location / {
           root   html;
           index  index.html index.htm;
       }
    }
```

# 四、Windows客户端安装证书
双击安装即可
选择将所有证书放在下列存储
受信任的根证书颁发机构



# 五、生成 PEM 格式的证书

如果您需要生成 PEM 格式的证书，使用以下命令可以从已有的证书和私钥中生成：

```shell
1、生成 PEM 格式的证书
openssl x509 -in server.crt -out server.pem -outform PEM
这个命令会将 server.crt 转换为 PEM 格式的 server.pem 证书。

2、生成 PEM 格式的私钥
openssl rsa -in server.key -out server-key.pem -outform PEM
这个命令会将 server.key 转换为 PEM 格式的 server-key.pem 私钥文件。

至此，您已经成功生成了 Tomcat 所需的证书和 PEM 格式的证书。
```

openssl pkcs12 -export -out server.p12  -inkey server.key  -in server.crt  -certpbe PBE-SHA1-3DES  -keypbe PBE-SHA1-3DES  -macalg sha1  -passout pass:123456



keytool -list -v -keystore server.p12 -storetype PKCS12 -storepass 您的密码

