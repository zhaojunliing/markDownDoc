

# **chrome允许javascript chrome允许使用摄像头**

最近在处理项目时发现新版谷歌浏览器不支持http协议调用本地摄像头，代码在本地运行下可以访问到摄像头，部署上线后网页中调用摄像头时失败。

对比本地和上线的区别后发现本地代码部署在IIS上时，访问是http:localhost而上线中是http+ip，

专门在网上查了一下，发现

1、http协议的网站的安全原因，不能访问本地的麦克风

2、允许打开麦克风的请求有http://localhost/以及https请求。

解决方法：

1、将网站部署成https请求

2、打开谷歌浏览器，在地址栏输入chrome://flags，

![chrome允许javascript chrome允许使用摄像头_解决方法](F:\github\markDownDoc\Chrome\assets\resize,m_fixed,w_1184.webp)

 

 设置后重启浏览器，输入网址可以正常调用摄像头。

3、右键点击Chrome桌面快捷方式，打开属性

在 快捷方式页-目标 中的最后输入

--unsafely-treat-insecure-origin-as-secure="http://example.com"
1.
http://example.com为要调用摄像头的网址

