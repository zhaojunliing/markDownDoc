安装freetds报错sql.h not found 处理办法：

1.编辑configure

vim ./configure

如下图所示：

找到$with_unixodbc中的

test -r "$ODBC_INC/sql.h" || as_fn_error $? "sql.h not found" "$LINENO" 5

这段代码

修改成

test -r "$ODBC_INC/sql.h" || as_fn_error $? "$ODBC_INC/sql.h not found" "$LINENO" 5

![img](F:\github\markDownDoc\Zabbix\assets\677a83dc76b148f3a442e177528c5e2a.png)

2.然后在编译，查看sql指向目录是哪里

![img](F:\github\markDownDoc\Zabbix\assets\8b719e8f55d24de680229fb6914a9d01.png)

3.核对报错中的/usr/local/unixODBC1/include/sql.h文件路径

没有则从unixODBC中的include复制过去

例如

ln -s /usr/local/unixODBC/include/sql.h  /usr/local/unixODBC1/include/sql.h

然后再重新编译

安装unixODBC、freetds 可参考之前写的文章