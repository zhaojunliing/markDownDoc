# FastAPI请求参数汇总

fastapi有各种各样的参数,包括:

- url参数(定义在url中的参数)
- param参数(使用url后面?xx=xx)定义的参数
- body参数(在请求主体中携带的json参数)
- form参数(在请求主体中携带的web表单参数)
- cookie参数(在请求的cookie中携带的参数)
- file参数(客户端上传的文件)

# 1. url参数

url参数是一种应用非常广泛的参数,很多地方都会使用,甚至简书的博客系统(看看你这篇文章的url)可能同样应用了这种类型的参数(但有可能不是这种实现).
 他的意思是,将信息放在url中,比如这样:
 https://www.jianshu.com/u/91bc345f7c84
 那么,我们就可以将后面的91bc345f7c84的部分定义为url参数,传递到函数中:
 例:



```python
from fastapi import FastAPI
app=FastAPI()
app.get("/echo/{text}")    #注意这里text将是函数定义的参数名.
async def getEchoApi(text:str):    #通过定义参数类型可以让fastapi进行默认的参数检查.
    return {"echo":text}
```

将其用uvicorn放到localhost:8000上执行.(不会的可以参见这篇文章[运行fastapi程序](https://www.jianshu.com/p/3345ec184adf))
 很简单的一段代码,将url中的内容原样返回.
 比如说,当我们访问这个url:
 localhost:8000/echo/HelloWorld
 返回的内容将是:
 {"echo":text}

# 2. param参数

这可能时最常见的一种参数了,比如说,当我们百度时,可以在地址栏上看到这样的地址:
 [https://www.baidu.com/s?ie=UTF-8&wd=fastapi参数汇总](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.baidu.com%2Fs%3Fie%3DUTF-8%26wd%3Dfastapi%E5%8F%82%E6%95%B0%E6%B1%87%E6%80%BB)
 其中,?后面的设定了参数ie为"UTF-8",参数wd为"fastapi"
 这种参数的定义是所有参数中最简单的,直接在函数定义中定义参数即可:



```python
from fastapi import FastAPI
app=FastAPI()
app.get("/echo2/")    #注意这里url上没有定义参数
async def getEchoApi(text:str):    #fastapi会聪明的发现它不是URL参数,然后自动将他识别为param参数.
    return {"echo":text}
```

运行后:
 localhost:8000/echo2?text=HelloWorld
 返回{"echo":text}

# 3. body参数

这种参数主要常见于数据的提交等操作中,注意get操作是不支持携带请求主体的,可以使用诸如POST,PUT等请求方式.
 对于这种参数的调试,我们可以使用POSTMAN进行.



```python
from fastapi import FastAPI
from pydantic import BaseModel    #fastapi的一个依赖,需要从pydantic中引入
app=FastAPI()
class EchoArgs(BaseModel):    #继承BaseModel
    text:str    #定义一个字符串型参数
app.post("/echo/")    #get不支持请求体,更换请求方法
async def postEchoApi(args:EchoArgs):    #设置刚才定义的参数
    return {"echo":item.text}    #原样返回
```

![img](https:////upload-images.jianshu.io/upload_images/20576062-660472c0748528ec.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

postman示意图.png



返回同之前的一样

# 4. form参数

这种参数主要常见于HTML中的表单,对于表单浏览器有额外的另一套规则,所以我们在这里要定义其参数类型为表单参数.



```python
from fastapi import FastAPI,Form    #引入Form用于定义表单参数
app=FastAPI()
app.post("/echo/") 
async def postEchoApi(text:str=Form(None)):    #通过Form设置参数
    return {"echo":text}    #原样返回
```

效果同之前的一样

# 5. cookie参数

cookie参数,一般用于用户的标识,用户习惯的记录等.各大网站都在使用这种方式,后来的session和token也要用到cookie技术.cookie本质是一种在客户端保存的数据,每次请求都会携带,你也可以在响应中进行设置.



```python
from fastapi import FastAPI,Cookie
app=FastAPI()
app.post("/echo/") 
async def postEchoApi(text:str=Cookie(None)):
    return {"echo":text}    #原样返回
```

可以发现,和form参数的定义十分相似.

# 6. file参数

file参数用于接收客户端上传的文件,有两种方式,



```python
from fastapi import FastAPI,File,UploadFile    #引入文件相关操作
app=FastAPI()
app.post("/upload/2") 
async def postUploadFile1Api(file:bytes=File(None)):
    ...    #文件相关操作
app.post("/upload/1")
async def postUploadFile2Api(file:UploadFile=File(None)):
    ...    #文件相关操作
```

其中,上传的文件,可以使用bytes或UploadFile两种格式,但我们推荐使用UploadFile方式,因为:

> Using `UploadFile` has several advantages over `bytes`:
>
> - It uses a "spooled" file:
>   - A file stored in memory up to a maximum size limit, and after passing this limit it will be stored in disk.
> - This means that it will work well for large files like images, videos, large binaries, etc. without consuming all the memory.
> - You can get metadata from the uploaded file.
> - It has a [file-like](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.python.org%2F3%2Fglossary.html%23term-file-like-object) `async` interface.
>    来自,fastapi官网.

大意:

- 它使用“假脱机”文件：
  - 存储在内存中的文件大小上限为最大，超过此限制后，文件将存储在磁盘中。
- 这意味着它可以很好地用于大型文件，例如图像，视频，大型二进制文件等，而不会占用所有内存。
- 您可以从上传的文件中获取元数据。
- 它具有类似类似文件的接口。
   所以推荐使用UploadFile



 以上是这篇文章的全部,错误的地方恳请指正.
 QQ:8955859,希望能和大家一起学习.
 最后,都看到这了,赞赏一下呗!(^ ~ ^||).



作者：冰中的火
链接：https://www.jianshu.com/p/9e720b1c367a
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。