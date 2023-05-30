# JDK环境变量配置

## 一、Windows下配置

### 1、系统环境变量配置
```shell
JAVA_HOME= C:\Program Files\Java\jdk1.7.0_80
CLASS_PATH= .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
--path的后面添加
;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
```
### 2、tomcat单独配置

```shell
在bin下增加setenv.bat
set JAVA_HOME=D:\web\jre1.8.0_101
set JRE_HOME=D:\web\jre1.8.0_101
set TITLE="ZNJK_Tomcat_9999"
set "JAVA_OPTS=%JAVA_OPTS% %LOGGING_CONFIG% -Dfile.encoding=UTF-8"
```


## 二、Linux下配置

### 1、配置系统环境变量
```shell
在etc/profile文件下添加
export JAVA_HOME=/usr/java/jdk1.7.0_25
export CLASSPATH=/usr/java/jdk1.7.0_25/lib
export PATH=$JAVA_HOME/bin:$PATH
```
### 2、配置用户环境变量

```shell
修改.bash_profile文件
```





