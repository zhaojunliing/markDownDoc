## 1. 准备软件

unixodbc-2.3.4    下载地址：http://www.unixodbc.org/

freetds  下载地址：http://www.freetds.org/index.html

## 2. 优先安装unixodbc

```shell
wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.11.tar.gz
tar -zxvf unixODBC-2.3.11.tar.gz 
cd unixODBC-2.3.11
./configure --prefix=/usr/local/unixODBC  
make && make install
```

```shell
#验证是否正常
odbcinst -j

#配置文件odbcinst.ini和odbc.ini
```

## 3. 安装freetds

```shell
tar zxf freetds-patched.tar.gz
cd freetds-1.3.18
#–prefix为设置FreeTDS的安装目录，–with-tdsver是设置TDS版本， –enable-msdblib为是否允许Microsoft数据库函数库
#TDS版本有4.2, 5.0, 7.0, 8.0
./configure --prefix=/usr/local/freetds --with-tdsver=5.0 --enable-msdblib --with-gnu-ld --enable-shared --enable-static --with-unixodbc=/usr/local
make && make install
```

(备注：以上编译参数如果需要了解请用 ./configure -h ) 

（编译如果出现：configure: error: sql.h not found ，可参考 安装freetds报错sql.h not found这篇文章处理）​

```shell


```

```shell
# 查看版本信息
/usr/local/freetds/bin/tsql -C

#连接sybase数据库
 /usr/local/freetds/bin/tsql -H 192.168.20.119 -p 5100 -U sa 
 
 
 
 isql -v sybase 'sa' 'compaq123'

```

