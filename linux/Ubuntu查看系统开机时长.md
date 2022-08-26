# Ubuntu上获取启动时间和正常运行时间

本文介绍了uptime命令用于实现此任务的用法。

```shell
linuxidc@linuxidc:~/linuxidc.com$ uptime -h

用法：
uptime [选项]

选项：
-p, --pretty  show uptime in pretty format
-h, --help    显示此帮助然后离开
-s, --since    system up since
-V, --version  显示程序版本然后离开
```

检查正常运行时间和启动时间

运行以下命令以查看自Ubuntu系统启动和运行(正常运行时间)以来的持续时间：

```shell
linuxidc@linuxidc:~/linuxidc.com$ uptime -p
up 5 hours, 20 minutes
```

输出显示时间，以小时和分钟为单位，以及是否有2个或更多用户登录。

运行以下命令以查看系统引导的时间(引导时间)：

```shell
linuxidc@linuxidc:~/linuxidc.com$ uptime -s
2018-11-26 07:56:29
```

输出显示系统引导的日期和时间。

另外还有一个参数 -V(大写)，是用来查询版本的。

```shell
linuxidc@linuxidc:~/linuxidc.com$ uptime -V
uptime from procps-ng 3.3.12
```

