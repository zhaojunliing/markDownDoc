# wondows下将tomcat注册为系统服务的相关操作

## 一、找到service.bat文件

确认你的tomcat\bin目录下，可以找到service.bat文件,

如果没有service.bat文件，那就下载文件，[官网地址](https://tomcat.apache.org/download-80.cgi)，注意选择对应的版本,

下载好将service.bat文件解压到你的tomcat\bin目录下。

## 二、注册

到你放置的目录下，打开命令行CMD，输入
```shell
service.bat install tomcat8
```

注意： install 后面是服务名， 你可以自己取名，最好根据tomcat版本取名。

## 三、启动

以管理员身份运行cmd，输入

```shell
sc start 服务名 例如：sc start tomcat8
```

即可启动服务。

## 四、关闭

以管理员身份运行cmd，输入

```shell
  sc stop 服务名 例如：sc stop tomcat8
```

即可关闭服务。

## 五、删除

以管理员身份运行cmd，输入

 ```
  sc delete 服务名 例如：sc delete tomcat8
 ```

即可删除服务。