# jdk安装信任自签名证书

最近服务端联调方式改为了https，使用了openssl自签名证书，但在windows上跑测试时，会报错

```
sun.security.validator.ValidatorException: PKIX path building failed: 
sun.security.provider.certpath.SunCertPathBuilderException: 
unable to find valid certification path to requested target
```

这是由于服务端用的自签名证书，不被jdk所信任，这时就需要将证书导入到jdk的keystore的信任列表里了，操作如下

### 1.导入证书

在**jdk\jre\lib\security**下面管理员执行命令行，将服务端的证书放在此目录下，导入到keystore，此处证书名为server.crt

```
keytool -importcert -alias server -keystore cacerts -file server.crt -keystore D:\env\java\jdk1.8.0_101\jre\lib\security\cacerts -storepass changeit
```

```bash
H:\java\jdk1.8.0_101\bin>keytool -import -alias server -file server.crt -keystore D:\env\java\jdk1.8.0_101\jre\lib\security\cacerts  -storepass changeit
所有者: CN=IP:192.168.10.161, O=fhgroup, L=ZhengZhou, ST=ZhengZhou, C=CN
发布者: CN=FHGROUP CA Center, O=fhgroup, L=ZhengZhou, ST=ZhengZhou, C=CN
序列号: 1e72477ce871bfc3a4854ae72df196d718d035ea
有效期开始日期: Thu Apr 25 17:31:58 CST 2024, 截止日期: Sat Apr 01 17:31:58 CST 2124
证书指纹:
         MD5: 69:77:F6:12:92:88:73:F8:65:3F:22:63:F8:DC:D3:93
         SHA1: 69:A2:11:6C:06:94:E7:29:B5:CC:B9:C5:6F:E1:90:82:C5:EE:B6:8B
         SHA256: 49:D9:7E:B2:CE:B3:A8:92:3C:CA:D0:FA:B7:5A:DF:02:2A:EA:82:15:12:49:15:C5:11:EB:1F:BA:51:76:F9:D7
         签名算法名称: SHA256withRSA
         版本: 3

扩展:

#1: ObjectId: 2.5.29.35 Criticality=false
AuthorityKeyIdentifier [
[CN=FHGROUP CA Center, O=fhgroup, L=ZhengZhou, ST=ZhengZhou, C=CN]
SerialNumber: [    563cd6a5 a060e789 723f7f04 303d21e4 45509a1d]
]

#2: ObjectId: 2.5.29.17 Criticality=false
SubjectAlternativeName [
  DNSName:
  DNSName:
  IPAddress: 192.168.10.161
]

#3: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 54 18 30 D4 3F 01 C8 D7   6C 22 69 D1 E4 B3 77 4F  T.0.?...l"i...wO
0010: 06 43 87 73                                        .C.s
]
]

是否信任此证书? [否]:  yes
错误的答案, 请再试一次
是否信任此证书? [否]:  y
证书已添加到密钥库中
```

输入命令后会提示输入密码，密码是changeit，之后会提示是否信任证，按y即可

**-keystore 路径为maven用的jdk的路径下面的**

### 2.查看证书列表

### 查看是否已加入到信任证书列表

```
keytool -list -v -keystore cacerts > java_cacerts.txt
```

这样受信任证书列表查询结果就导入到java_cacetrs.txt中了，在文件中查找刚自己的证书名，查到即可

### 3.删除证书

如果在添加过程中出错，需要删除证书，使用以下命令

```
keytool -delete -alias server
```

### 4.查看指定证书

单独查看自己的证书

```
keytool -list -v -alias server
```

