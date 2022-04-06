## 常用的选择器

```cpp
chromedp.BySearch // 如果不写，默认会使用这个选择器，类似devtools ctrl+f 搜索
chromedp.ByID // 只id来选择元素
chromedp.ByQuery // 根据document.querySelector的规则选择元素，返回单个节点
chromedp.ByQueryAll // 根据document.querySelectorAll返回所有匹配的节点
chromedp.ByNodeIP // 检索特定节点(必须先有分配的节点IP)，这个暂时没用过也没看到过例子，如果有例子可以发给我看下
```

## 常用api

```go
chromedp.Navigate("https://xxxx") // 跳转的url
chromedp.WaitVisible(`#username`, chromedp.ByID), //  使用chromedp.ByID选择器。所以就是等待id=username的标签元素加载完。
chromedp.SendKeys(`#username`, "username", chromedp.ByID), // 使用chromedp.ByID选择器。向id=username的标签输入username。
chromedp.SetValue 设置值，和sendkey不同的是，如果原有的输入框中有内容，sendkey不会清空原来的值，setva
chromedp.Value(`#input1`, val1, chromedp.ByID), // 获取id=input1的值，并把值传给val1
chromedp.Click("btn-submit",chromedp.Bysearch), // 触发点击事件，
chromedp.Screenshot(`#row`, &buf, chromedp.ByID), // 截图id=row的标签，把值传入buf 需要事先定义var buf []byte 
chromedp.ActionFunc(func(context.Context, cdp.Executor) error { // 将图片写入文件
 return ioutil.WriteFile("test.png", buf, 0644)
}),
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