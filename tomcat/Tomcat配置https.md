#Tomcat配置https步骤
##1、为服务器生成证书
进入控制台，切换到%JAVA_HOME%/bin目录，执行如下命令：
```
keytool -genkey -v -alias tomcat -keyalg RSA -keystore D:\home\tomcat.keystore -validity 36500
```
示例如下：


参数简要说明：“D:\home\tomcat.keystore”含义是自定义的证书文件的保存路径，证书文件名称是tomcat.keystore ；“-validity 36500”含义是证书有效期，36500表示100年，默认值是90天 “tomcat”为自定义证书名称。

执行之后需要填写一下必要参数：
- A、输入keystore密码：此处需要输入大于6个字符的字符串；
- B、“您的名字与姓氏是什么？”这是必填项，并且必须是TOMCAT部署主机的域名或者IP[如：gbcom.com 或者 10.1.25.251]（就是你将来要在浏览器中输入的访问地址），否则浏览器会弹出警告窗口，提示用户证书与所在域不匹配。在本地做开发测试时，应填入“localhost”；
- C、你的组织单位名称是什么？”、“您的组织名称是什么？”、“您所在城市或区域名称是什么？”、“您所在的州或者省份名称是什么？”、“该单位的两字母国家代码是什么？”可以按照需要填写也可以不填写直接回车，在系统询问“正确吗？”时，对照输入信息，如果符合要求则使用键盘输入字母“y”，否则输入“n”重新填写上面的信息。
- D、输入<tomcat>的主密码，这项较为重要，会在tomcat配置文件中使用，建议输入与keystore的密码一致，设置其它密码也可以，完成上述输入后，直接回车则在你在第二步中定义的位置找到生成的文件。

## 2、为客户端生成证书
为浏览器生成证书，以便让服务器来验证它。为了能将证书顺利导入至IE和Firefox，证书格式应该是PKCS12，因此，使用如下命令生成：

```
keytool -genkey -v -alias mykey -keyalg RSA -storetype PKCS12 -keystore D:\home\mykey.p12
```

mykey为自定义，对应的证书库存放在“D:\home\mykey.p12”，客户端的CN可以是任意值。双击mykey.p12文件，即可将证书导入至浏览器（客户端）。

## 3.让服务器信任客户端证书
由于是双向SSL认证，服务器必须要信任客户端证书，因此，必须把客户端证书添加为服务器的信任认证。由于不能直接将PKCS12格式的证书库导入，必须先把客户端证书导出为一个单独的CER文件，使用如下命令：
```
keytool -export -alias mykey -keystore D:\home\mykey.p12 -storetype PKCS12 -storepass password -rfc -file D:\home\mykey.cer
```

(mykey为自定义与客户端定义的mykey要一致，password是你设置的密码)。通过以上命令，客户端证书就被我们导出到“D:\home\mykey.cer”文件了。
下一步，是将该文件导入到服务器的证书库，添加为一个信任证书使用命令如下：
```
keytool -import -v -file D:\home\mykey.cer -keystore D:\home\tomcat.keystore
```

通过list命令查看服务器的证书库，可以看到两个证书，一个是服务器证书，一个是受信任的客户端证书：
```
keytool -list -keystore D:\home\tomcat.keystore
```
(tomcat是你设置服务器端的证书名)。

##4、让客户端信任服务器证书
由于是双向SSL认证，客户端也要验证服务器证书，因此，必须把服务器证书添加到浏览的“受信任的根证书颁发机构”。由于不能直接将keystore格式的证书库导入，必须先把服务器证书导出为一个单独的CER文件，使用如下命令：
```
keytool -keystore D:\home\tomcat.keystore -export -alias tomcat -file D:\home\tomcat.cer
```

通过以上命令，服务器证书就被我们导出到“D:\home\tomcat.cer”文件了。双击tomcat.cer文件，按照提示安装证书，将证书填入到“受信任的根证书颁发机构”。

## 5、配置tomcat服务器
一共需要修改两个配置文件server.xml与web.xml

###1）、打开tomcat配置文件，如：D:/apache-tomcat-6.0.29/conf/server.xml，修改如下：
①原：
```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```
修改后：
```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="443" />
```

②原：
```
<!--
<Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
              maxThreads="150" scheme="https" secure="true"
              clientAuth="false" sslProtocol="TLS"/>
 -->
```
修改后（去掉注释且修改参数）：
```
<Connector port="443" protocol="HTTP/1.1" SSLEnabled="true"
               maxThreads="150" scheme="https" secure="true"
               clientAuth="false" sslProtocol="TLS" keystoreFile="E:/tomcat.keystore" keystorePass="123456789"/>
```
注释： 
keystoreFile、keystorePass 两个参数，分别是证书文件的位置和<tomcat>的主密码，在证书文件生成过程中做了设置

③原：
```
<!--
   <Connector port="8009" enableLookups="false" protocol="AJP/1.3" redirectPort="8443" />-->
```
修改后（去掉注释且修改参数）：
```
<Connector port="8009" enableLookups="false" protocol="AJP/1.3" redirectPort="443" />
```

###2）打开D:/apache-tomcat-6.0.29/conf/web.xml，在该文件</welcome-file-list>后面加上这样一段：
```
<login-config> 
<!-- Authorization setting for SSL --> 
<auth-method>CLIENT-CERT</auth-method> 
<realm-name>Client Cert Users-only Area</realm-name> 
</login-config> 

<security-constraint> 
<!-- Authorization setting for SSL --> 
<web-resource-collection > 
<web-resource-name >SSL</web-resource-name> 
<url-pattern>/*</url-pattern> 
</web-resource-collection> 
<user-data-constraint> 
<transport-guarantee>CONFIDENTIAL</transport-guarantee> 
</user-data-constraint> 
</security-constraint>
```