# Air 热加载使用

## 1、下载安装

```shell
$ go install github.com/cosmtrek/air@latest
```

> 注意：这里要用install，而不是get，install会生成可执行文件，并且保证你的Gopath/bin已经配置到了系统环境变量下面

## 2、初始化

```shell
#查找你的项目中是否存在 `.air.conf` 配置文件
$ air -c .air.conf
#初始化一个配置文件
$ air init
```

会生成一个air的配置文件.air.toml，内容如下：

```toml
会生成一个air的配置文件.air.toml，内容如下：

root = "."
testdata_dir = "testdata"
tmp_dir = "tmp"

[build]
  args_bin = []
  bin = "tmp\\main.exe"
  cmd = "go build -o ./tmp/main.exe ."
  delay = 1000
  exclude_dir = ["assets", "tmp", "vendor", "testdata"]
  exclude_file = []
  exclude_regex = ["_test.go"]
  exclude_unchanged = false
  follow_symlink = false
  full_bin = ""
  include_dir = []
  include_ext = ["go", "tpl", "tmpl", "html"]
  kill_delay = "0s"
  log = "build-errors.log"
  send_interrupt = false
  stop_on_error = true

[color]
  app = ""
  build = "yellow"
  main = "magenta"
  runner = "green"
  watcher = "cyan"

[log]
  time = false

[misc]
  clean_on_exit = false

[screen]
  clear_on_rebuild = false
```

## 3、相关命令

```shell
# 启动热加载
$ air 
# 启动热加载 带打印log
$ air -d
```

### 4、运行时参数

您可以通过把变量添加在 air 命令之后来传递参数。

```
# 会执行 ./tmp/main bench
air bench

# 会执行 ./tmp/main server --port 8080
air server --port 8080
```



You can separate the arguments passed for the air command and the built binary with `--` argument.

```
# 会运行 ./tmp/main -h
air -- -h

# 会使用个性化配置来运行 air，然后把 -h 后的变量和值添加到运行的参数中
air -c .air.toml -- -h
```