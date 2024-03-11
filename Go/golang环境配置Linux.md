# golang环境配置Linux

- Golang官网 https://golang.org/
- 安装版本连接 https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
- 安装命令：

```
// 下载源码安装包
wget https://go.dev/dl/go1.22.0.linux-amd64.tar.gz
// 解压源码
sudo tar -C /usr/local -xzf go1.22.0.linux-amd64.tar.gz

// 用户级别配置
sudo vi $HOME/.profile

export PATH=$PATH:/usr/local/go/bin
#设置环境变量
export GOROOT=/usr/local/go
export GOPATH=$HOME/go

// 生效配置
source $HOME/.profile
```

- 传统hello world例子，hello.go

```golang
package main

import "fmt"   // 我们需要使用fmt包中的Println()函数

func main() {
    fmt.Println("Hello, world.    你好，世界！")
}
```

> 参考官网 https://golang.org/doc/install