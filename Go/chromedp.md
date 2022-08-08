## 常用的选择器

```cpp
chromedp.BySearch // 如果不写，默认会使用这个选择器，类似devtools ctrl+f 搜索
chromedp.ByID // 只id来选择元素
chromedp.ByQuery // 根据document.querySelector的规则选择元素，返回单个节点
chromedp.ByQueryAll // 根据document.querySelectorAll返回所有匹配的节点
chromedp.ByNodeIP // 检索特定节点(必须先有分配的节点IP)，这个暂时没用过也没看到过例子，如果有例子可以发给我看下
    
chromedp.ByJSPath
chromedp.ByNodeID
chromedp.ByFunc
```

## 常用api

```go
chromedp.Navigate("https://xxxx") // 跳转的url
chromedp.WaitVisible(`#username`, chromedp.ByID), //  使用chromedp.ByID选择器。所以就是等待id=username的标签元素加载完。
chromedp.SendKeys(`#username`, "username", chromedp.ByID), // 使用chromedp.ByID选择器。向id=username的标签输入username。
chromedp.SetValue //设置值，和sendkey不同的是，如果原有的输入框中有内容，sendkey不会清空原来的值，setva
chromedp.Value(`#input1`, val1, chromedp.ByID), // 获取id=input1的值，并把值传给val1
chromedp.Click("btn-submit",chromedp.Bysearch), // 触发点击事件，
chromedp.Screenshot(`#row`, &buf, chromedp.ByID), // 截图id=row的标签，把值传入buf 需要事先定义var buf []byte 
chromedp.ActionFunc(func(context.Context, cdp.Executor) error { // 将图片写入文件
 return ioutil.WriteFile("test.png", buf, 0644)
}),


chromedp.NewContext() 初始化chromedp的上下文，后续这个页面都使用这个上下文进行操作
chromedp.Run() 运行一个chrome的一系列操作
chromedp.Navigate() 将浏览器导航到某个页面
chromedp.WaitVisible() 等候某个元素可见，再继续执行。
chromedp.Click() 模拟鼠标点击某个元素
chromedp.Value() 获取某个元素的value值
chromedp.ActionFunc() 再当前页面执行某些自定义函数
chromedp.Text() 读取某个元素的text值
chromedp.Evaluate() 执行某个js，相当于控制台输入js
network.SetExtraHTTPHeaders() 截取请求，额外增加header头
chromedp.SendKeys() 模拟键盘操作，输入字符
chromedp.Nodes() 根据xpath获取某些元素，并存储进入数组
chromedp.NewRemoteAllocator
chromedp.OuterHTML() 获取元素的outer html
chromedp.Screenshot() 根据某个元素截图
page.CaptureScreenshot() 截取整个页面的元素
chromedp.Submit() 提交某个表单
chromedp.WaitNotPresent() 等候某个元素不存在，比如“正在搜索。。。”
chromedp.Tasks{} 一系列Action组成的任务

熟悉最常用的几个方法：

chromedp.NewContext() 初始化chromedp的上下文，后续这个页面都使用这个上下文进行操作

chromedp.Run() 运行一个chrome的一系列操作

chromedp.Navigate() 将浏览器导航到某个页面

chromedp.WaitVisible() 等候某个元素可见，再继续执行。

chromedp.Click() 模拟鼠标点击某个元素

chromedp.Value() 获取某个元素的value值

chromedp.ActionFunc() 再当前页面执行某些自定义函数

chromedp.Text() 读取某个元素的text值

chromedp.Evaluate() 执行某个js，相当于控制台输入js

network.SetExtraHTTPHeaders() 截取请求，额外增加header头

chromedp.SendKeys() 模拟键盘操作，输入字符

chromedp.Nodes() 根据xpath获取某些元素，并存储进入数组

chromedp.NewRemoteAllocator

chromedp.OuterHTML() 获取元素的outer html

chromedp.Screenshot() 根据某个元素截图

page.CaptureScreenshot() 截取整个页面的元素

chromedp.Submit() 提交某个表单

chromedp.WaitNotPresent() 等候某个元素不存在，比如“正在搜索。。。”

```

## headless_shell（chrome）Flag

```shell
--no-first-run 第一次不运行
---default-browser-check 不检查默认浏览器
--headless 不开启图像界面
--disable-gpu 关闭gpu,服务器一般没有显卡
remote-debugging-port chrome-debug工具的端口(golang chromepd 默认端口是9222,建议不要修改)
--no-sandbox 不开启沙盒模式可以减少对服务器的资源消耗,但是服务器安全性降低,配和参数 --remote-debugging-address=127.0.0.1 一起使用
--disable-plugins 关闭chrome插件
--remote-debugging-address 远程调试地址 0.0.0.0 可以外网调用但是安全性低,建议使用默认值 127.0.0.1
--window-size 窗口尺寸
```

# 例子

```csharp
func clickPermission(url string,permission string) chromedp.Tasks {
    return chromedp.Tasks{
        // 这里直接用click点击下拉框中的用户组但是却不生效，不知道为什么采用在input中输入相关用户组搜索，然后发送kb.down和kb.enter来选择
        chromedp.Navigate(url),
        chromedp.Click(`input[placeholder="选择需要授权的用户组 （可输入进行搜索）"]`, chromedp.BySearch),
        chromedp.SendKeys(`input[placeholder="选择需要授权的用户组 （可输入进行搜索）"]`,permission ,chromedp.BySearch), // 输入 对应的用户组
        chromedp.Sleep(time.Second),
        chromedp.SendKeys(`input[placeholder="选择需要授权的用户组 （可输入进行搜索）"]`,kb.ArrowDown+kb.Enter ,chromedp.BySearch), // 输入down和enter
        chromedp.Sleep(1 * time.Second),
        chromedp.Click("#form1 > div.layui-form.layui-border-box.layui-table-view > div.layui-table-box > div.layui-table-body.layui-table-main > table > tbody > tr > td:nth-child(6) > div > div:nth-child(1)",chromedp.ByQuery), // 点击读写权限按钮
// 这个是找不到对应的值在chrome里复制selector得到的
        chromedp.Click(`button[lay-event="save"]`,chromedp.ByQuery), // 点击读写权限按钮
        chromedp.Click(`a[class="layui-layer-btn0"]`,chromedp.ByQuery), // 点击提交按钮
        chromedp.WaitVisible(`div[class="layui-layer-content layui-layer-padding"]`,chromedp.BySearch),
        chromedp.Sleep(2 * time.Second),
    }
}
```

调用



```cpp
        err = chromedp.Run(ctx,clickPermission(nUrl,baseInfo.Permission))
        if err != nil {
            log.Fatal(err)
        }
```