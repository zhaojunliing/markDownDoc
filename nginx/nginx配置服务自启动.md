1、先创建开机自启脚本（nginx.service）

```
cd /etc/systemd/system
vi nginx.service
```

2、nginx.service文件内容

```
# 仅修改 /usr/local/nginx/sbin/nginx 这个路径即可（修改为你的nginx路径）

[Unit]
Description=nginx service
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

3、设置文件权限

```
chmod 755 nginx.service
```

4、设置开机自启动

```
systemctl daemon-reload
systemctl enable nginx
```

5、先用此命令启停一下 看配置是否正常

```
systemctl start nginx # nginx启动
systemctl stop nginx # nginx停止
```

6、直接重启服务器即可（nginx就自动重启了）

```
reboot
```

7、备注

```
# 启动nginx服务
systemctl start nginx.service

# 重新启动nginx服务
systemctl restart nginx.service

# 查看nginx服务当前状态
systemctl status nginx.service

# 停止开机自启动
systemctl disable nginx.service
```