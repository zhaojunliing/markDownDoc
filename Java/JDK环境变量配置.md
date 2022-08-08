# JDK环境变量配置

## 一、Windows下配置

```shell
JAVA_HOME= C:\Program Files\Java\jdk1.7.0_80
CLASS_PATH= .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
--path的后面添加
;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```

## 二、Linux下配置

```shell
在etc/profile文件下添加
export JAVA_HOME=/usr/java/jdk1.7.0_25
export CLASSPATH=/usr/java/jdk1.7.0_25/lib
export PATH=$JAVA_HOME/bin:$PATH
```



