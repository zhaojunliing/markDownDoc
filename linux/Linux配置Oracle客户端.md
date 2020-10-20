Linux配置Oracle客户端

将连接oracle的python代码打包成exe文件给同事用时报错：`Error: DPI-1047: Cannot locate a 64-bit Oracle Client library: "The specified module could not be found". See https://oracle.github.io/odpi/doc/installation.html#windows for help`



#### windows解决办法一：

1、安装oracle 64位客户端
2、配置环境path，将客户端的目录，比如d:\oracle\product\11.2.0\client_1\bin 配置到path中
3、前面两步做完，执行报了另一个错 ora-01804 ，将 ORACLE_HOME目录设置为 d:\oracle\product\11.2.0\client_1 后，问题解决。

#### windows解决办法二：

1、下载轻量级的Oracle客户端：
      比如 instantclient-basic-windows.x64-11.2.0.4.0.zip
2、配置环境变量path：
      将客户端的目录，比如 D:\oracle\instantclient_11_2 添加到 path
3、配置环境变量ORACL_HOME：
     将客户端的上一级目录 ，比如 D:\oracle 设置为ORACLE_HOME的值
4、上面3步做完，执行报错：```nls_lan python Error: 'ascii' codec can't encode characters in position 28-31: ordinal not in range(128),```
    然后添加环境变量NLS_LANG，设置为：AMERICAN_AMERICA.ZHS16GBK，其值是数据库nls_database_parameters中NLS_LANGUAGE，NLS_TERRITORY，NLS_CHARACTERSET分别对应的值；
    设置完成后执行成功。

#### linux系统python连接Oracle报错的解决办法1：

1、下载轻量级的oracle客户端并解压安装
2、配置环境变量LD_LIBRARY_PATH=[oracle客户端安装目录]:$LD_LIBRARY_PATH ，NLS_LANG=[oracle数据库NLS_LANGUAGE_NLS_TERRITORY.NLS_CHARACTERSET] ,并添加到 ~/.bash_profile 中
3、重新登录系统 或 source ~/.bash_profile 使环境变量生效
4、重新执行后成功