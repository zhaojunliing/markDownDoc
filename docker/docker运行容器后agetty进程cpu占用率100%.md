# docker运行容器后agetty进程cpu占用率100%

1、查看agetty进程是哪个容器持有的
命令：

```shell
docker ps -q | xargs docker inspect --format '{{.State.Pid}}, {{.Name}}' | grep $(ps -ef|grep <pid> |grep -v grep |awk '{print $3}')
```


例子：top查看agetty进程PID是61822，则将上面命令字符串中的<pid>替换为61822

则实际的命令为：

```shell
docker ps -q | xargs docker inspect --format '{{.State.Pid}}, {{.Name}}' | grep $(ps -ef|grep 61822 |grep -v grep |awk '{print $3}')
```

执行后查到容器如下：

```shell
6573, /dev
```

2、出于对安全的考虑，在启动容器时，docker容器里的系统只具有一些普通的linux权限，并不具有真正root用户的所有权限。而--privileged=true参数可以让docker容器具有linux root用户的所有权限。

为了解决这个问题，docker后来的版本中docker run增加了两个选项参数"--cap-add"和"--cap-drop"。

--cap-add : 获取default之外的linux的权限

--cap-drop: 放弃default linux权限

从docker官网的文档中可以查到，docker容器具有的default权限及--cap-add可以获取到的扩展权限如下：

Default 权限：

| Capability Key   | Capability Description                                       |
| ---------------- | ------------------------------------------------------------ |
| SETPCAP          | Modify process capabilities.                                 |
| MKNOD            | Create special files using mknod(2).                         |
| AUDIT_WRITE      | Write records to kernel auditing log.                        |
| CHOWN            | Make arbitrary changes to file UIDs and GIDs (see chown(2)). |
| NET_RAW          | Use RAW and PACKET sockets.                                  |
| DAC_OVERRIDE     | Bypass file read, write, and execute permission checks.      |
| FOWNER           | Bypass permission checks on operations that normally requirethe file system UID of the process to match the UID of the file. |
| FSETID           | Don’t clear set-user-ID and set-group-ID permission bitswhen a file is modified. |
| KILL             | Bypass permission checks for sending signals.                |
| SETGID           | Make arbitrary manipulations of process GIDs and supplementaryGID list. |
| SETUID           | Make arbitrary manipulations of process UIDs.                |
| NET_BIND_SERVICE | Bind a socket to internet domain privileged ports(port numbers less than 1024). |
| SYS_CHROOT       | Use chroot(2), change root directory.                        |
| SETFCAP          | Set file capabilities.                                       |

通过--cap-add获取到的权限：

| Capability Key  | Capability Description                                       |
| --------------- | ------------------------------------------------------------ |
| SYS_MODULE      | Load and unload kernel modules.                              |
| SYS_RAWIO       | Perform I/O port operations (iopl(2) and ioperm(2)).         |
| SYS_PACCT       | Use acct(2), switch process accounting on or off.            |
| SYS_ADMIN       | Perform a range of system administration operations.         |
| SYS_NICE        | Raise process nice value (nice(2), setpriority(2)) andchange the nice value for arbitrary processes. |
| SYS_RESOURCE    | Override resource Limits.                                    |
| SYS_TIME        | Set system clock (settimeofday(2), stime(2), adjtimex(2));set real-time (hardware) clock. |
| SYS_TTY_CONFIG  | Use vhangup(2); employ various privileged ioctl(2) operationson virtual terminals. |
| AUDIT_CONTROL   | Enable and disable kernel auditing; change auditing filter rules;retrieve auditing status and filtering rules. |
| MAC_OVERRIDE    | Allow MAC configuration or state changes.Implemented for the Smack LSM. |
| MAC_ADMIN       | Override Mandatory Access Control (MAC). Implemented for the Smack Linux Security Module (LSM). |
| NET_ADMIN       | Perform various network-related operations.                  |
| SYSLOG          | Perform privileged syslog(2) operations.                     |
| DAC_READ_SEARCH | Bypass file read permission checks and directory read andexecute permission checks. |
| LINUX_IMMUTABLE | Set the FS_APPEND_FL and FS_IMMUTABLE_FL i-node flags.       |
| NET_BROADCAST   | Make socket broadcasts, and listen to multicasts.            |
| IPC_LOCK        | Lock memory (mlock(2), mlockall(2), mmap(2), shmctl(2)).     |
| IPC_OWNER       | Bypass permission checks for operations on System V IPC objects. |
| SYS_PTRACE      | Trace arbitrary processes using ptrace(2).                   |
| SYS_BOOT        | Use reboot(2) and kexec_load(2), reboot and load a new kernelfor later execution. |
| LEASE           | Establish leases on arbitrary files (see fcntl(2)).          |
| WAKE_ALARM      | Trigger something that will wake up the system.              |
| BLOCK_SUSPEND   | Employ features that can block system suspend.               |

所以，在运行容器时，可以不用--privileged参数的尽量不用，用--cap-add参数替代。如果必须使用--privileged=true参数的，可以通过在宿主机和容器中执行以下命令将agetty关闭。

```
shell> systemctl stop getty@tty1.service

shell> systemctl mask getty@tty1.service
```

 

参考资料：

https://github.com/docker/docker/issues/4040

https://docs.docker.com/engine/reference/run/