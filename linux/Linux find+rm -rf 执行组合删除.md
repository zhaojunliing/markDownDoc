# Linux find+rm -rf 执行组合删除

Linux find+rm -rf  执行组合删除

**[  语法  ]**

```shell
find 目录 -mtime + 天数 -name "文件名称" -exec rm -rf {} \;
```

**[  举例说明  ]** 

```shell
find /data/tomcat/logs/ -mtime +30 -name "catalina.*" -exec rm -rf {} \;
```

\--  将  /data/tomcat/logs/  目录下所有  30  天前带  "catalina."  的文件删除



**[  参数说明  ]** 

| 参数               | 说明                                      |
| ------------------ | ----------------------------------------- |
| find               | linux  的查找命令，用户查找指定条件的文件 |
| /data/tomcat/logs/ | 想要进行清理的任意目录                    |
| -mtime             | 标准语句写法                              |
| +30                | 查找 30 天前的文件，这里用数字代表天数    |
| catalina.*         | 希望查找的数据类型，"*"  表示查找所有文件 |
| -exec              | 固定写法                                  |
| rm -rf             | 强制删除文件，包括目录                    |
| {} \;              | 固定写法，一对大括号  \+  空格  +\        |

详细的说明见Linux下find的使用说明