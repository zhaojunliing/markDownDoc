# Windows 客户端https证书安装

### 一、下载文件

点击提供的客户端证书下载链接下载证书文件

### 二、安装证书

用鼠标双击下载的证书文件，弹出安装证书界面，点击安装证书按钮，如下图

![image-20230822101406731](F:\github\markDownDoc\其他\assets\image-20230822101406731.png)

### 三、证书导入

默认选择当前用户，并点击下一步

![image-20230822101559160](F:\github\markDownDoc\其他\assets\image-20230822101559160.png)

### 四、选择证书位置

选择将所有的证书都放入下列存储，然后点击浏览，在弹出的窗口选择受信任的根证书颁发机构，并点击确定，弹出窗口关闭后，点击下一步按钮

![image-20230822101718595](F:\github\markDownDoc\其他\assets\image-20230822101718595.png)

### 五、完成导入

点击完成按钮即可

![image-20230822101815761](F:\github\markDownDoc\其他\assets\image-20230822101815761.png)

### 六、安全警告

会显示证书来源，确认来源没问题后点击是即可



![image-20230822102132335](F:\github\markDownDoc\其他\assets\image-20230822102132335.png)

### 七、导入成功

![image-20230822102223334](F:\github\markDownDoc\其他\assets\image-20230822102223334.png)

### 八、证书情况

上一步导入成功后，可以查看证书机构和有效期信息

![image-20230822102406402](F:\github\markDownDoc\其他\assets\image-20230822102406402.png)

### 附：证书删除

```shell
运行 certmgr.msc命令，可打开证书管理窗口，选择对应的证书删除即可
```

