#Ubuntu20.04 设置frp开机自启动

在`/etc/systemd/system/`目录下创建服务文件

```bash
cd /etc/systemd/system/
sudo vim frpc.service
```

填入以下信息，`ExecStart`自行替换为你自己的frp启动命令

```shell
[Unit]
Description=frpc
After=network.target
Wants=network.target

[Service]
Restart=on-failure
RestartSec=5
ExecStart=/home/zzj/Software/frp_0.36.2_linux_amd64/frpc -c /home/zzj/Software/frp_0.36.2_linux_amd64/frpc.ini

[Install]
WantedBy=multi-user.target

# 刷新服务列表
systemctl daemon-reload
# 设置开机自启
systemctl enable frpc.service
# 启动服务
systemctl start frpc.service
```

执行上述命令服务就可以设置开机自启并启动服务

- 服务可能会在开机时启动失败。因此在设置开机自启命令时，最好在`[Service]`中定义`Restart`和`RestartSec`。
- 下面是一些常用的`systemctl`命令

```bash
# 关闭开机自启
systemctl disable frpc.service
# 停止服务
systemctl stop frpc.service
# 重启服务
systemctl restart frpc.service
# 查看状态
systemctl status frpc.service
# 查看是否设置开机自启
systemctl is-enabled frpc.service
```