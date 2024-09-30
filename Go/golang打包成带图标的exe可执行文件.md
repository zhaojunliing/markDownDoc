在Go语言（Golang）中构建生成的可执行文件（EXE）并添加自定义图标，你可以按照以下步骤操作：

### 1. 准备图标文件

首先，你需要一个图标文件（通常是`.ico`格式）。确保这个图标符合Windows应用程序的标准，例如，可以使用256x256像素的尺寸。

### 2. 创建资源文件

使用图标文件创建一个资源脚本文件（通常以`.rc`为扩展名）。在这个文件中，你需要定义图标与其对应的资源标识符。例如，如果你的图标文件名为`app.ico`，资源文件（`app.rc`）的内容可能如下：

```
// 定义图标资源
IDR_MAINICON ICON "app.ico"
```

### 3. 编译资源文件

使用资源编译器（如`windres`，这是MinGW或Cygwin中的工具）将资源脚本编译成二进制资源文件。在命令行中执行如下命令：

```
windres -o appres.res app.rc
```

这将生成一个名为`appres.res`的资源文件。

### 4. 构建Go程序

在构建Go程序时，包括这个资源文件。如果你使用的是`go build`命令，可以通过以下方式将资源文件链接到最终的EXE中：

```
go build -o myapp.exe -ldflags="-H windowsgui" main.go
gcc -o myapp.exe myapp.o appres.res -mwindows
```

这里的命令假设你的Go程序入口是`main.go`，并且你已经编译过Go代码生成了相应的`myapp.o`对象文件。`-mwindows`选项确保程序在不打开命令行窗口的情况下运行。

### 5. 测试

生成的EXE文件应该已经包含了自定义图标。你可以在Windows资源管理器中查看EXE文件的图标是否已经更新。

### 注意事项

- 确保在构建过程中使用的图标文件路径正确无误。
- 这个方法需要你有一定的命令行工具使用经验，包括Go工具链和MinGW或Cygwin工具。
- 如果在Windows上没有安装GCC，可能需要先安装MinGW或其他提供`gcc`和`windres`的工具集。

通过这些步骤，你可以为用Go编写的Windows应用程序添加自定义图标。