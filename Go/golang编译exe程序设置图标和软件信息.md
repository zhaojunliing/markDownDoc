# golang编译exe程序设置图标和软件信息

前期准备一张ico图标，以下操作均为windows下进行

## 一、下载goversioninfo

```shell
go get github.com/josephspurrier/goversioninfo
```

程序会下载到%GOPATH%/pkg/mod/github.com/josephspurrier/goversioninfo@v1.4.0

## 二、编译goversioninfo为exe

进入%GOPATH%/pkg\mod\github.com\josephspurrier\goversioninfo@v1.4.0\cmd\goversioninfo路径下

编译生成可执行文件

```shell
go build -o %GOPATH%\bin\goversioninfo.exe main.go
```
## 三、配置环境变量

在环境变量PATH中添加%GOPATH%\bin

## 四、添加versioninfo.json

复制versioninfo.json到自己的项目目录下，在testdata\example32和testdata\example36目录下有，随便复制一个，并将其中StringFileInfo下的参数修改为自己实际参数，设置IconPath为ico名称，例如

"IconPath": "demo.ico", //ico、main.go、配置文件都在同级目录中

## 五、声明

在自己项目的main.go文件顶部增加备注行//go:generate goversioninfo -icon=demo.ico，其中icon文件请修改为自己的文件名称

## 六、生成资源文件

goversioninfo文档介绍说，执行go generate即可，实际使用发现一直报错，后直接执行generate成功生成resource.syso

## 七、编译

最后执行go build执行编译即可，右键查看属性吧

运行时隐藏golang程序自己的cmd窗口：
go build -ldflags -H=windowsgui 