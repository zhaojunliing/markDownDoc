# Python - loguru日志库，高效输出控制台日志和日志记录

## 1、安装loguru

loguru的PyPI地址为：https://pypi.org/project/loguru/

GitHub仓库地址为：https://github.com/Delgan/loguru

我们可以直接使用pip命令对其进行安装

pip install loguru 

或者下载其源码，使用Python命令进行安装。

## 2、loguru简单使用

```python
from loguru import logger

logger.info("中文loguru")
logger.debug("中文loguru")
logger.error("中文loguru")
logger.warning("中文loguru")
```

```python
2020-03-07 15:51:05.752 | INFO     | __main__:info:23 - 中文loguru
2020-03-07 15:51:05.753 | DEBUG    | __main__:debug:26 - 中文loguru
2020-03-07 15:51:05.753 | WARNING  | __main__:warning:29 - 中文loguru
2020-03-07 15:51:05.753 | ERROR    | __main__:error:32 - 中文loguru
```

可以看到其默认的输出格式是包含【时间、级别、模块名、行号以及日志信息】，不需要手动创建 logger ，直接使用即可，另外其输出还是彩色的，看起来会更加友好。

## 3、loguru保留日志文件

一般情况，我们都需要将日志输出保存到文件中，loguru直接通过 add() 方法，就可以配置一个日志文件，如下代码所示：

```python
# coding:utf-8
from loguru import logger

logger.add("interface_log_{time}.log", rotation="500MB", encoding="utf-8", enqueue=True, compression="zip", retention="10 days")
logger.info("中文test")
logger.debug("中文test")
logger.error("中文test")
logger.warning("中文test")

```

包含知识点：

- 第一个参数是保存日志信息的文件路径，像我写的后缀多了个 {time} ，就是获取当前时间节点，这样就会自动创建新的日志；这个time应该是库里自带的变量，如果你想自己定义time也可以的哦，具体可以看看下面封装类的实现形式！

- 当你需要输出中文日志的时候，请加上 encoding="utf-8" ，避免出现乱码 

- enqueue=True 代表异步写入，官方的大概意思是：在多进程同时往日志文件写日志的时候使用队列达到异步功效

- rotation

   可以理解成日志的创建时机，可以有多种写法

  - rotation="500 MB" ：当日志文件达到500MB时就会重新生成一个文件
  - rotation="12:00" ：每天12点就会创建新的文件、
  - rotation="1 week" ：每隔一周创建一个log

- retention 配置日志的最长保留时间，官方例子： "1 week, 3 days"、"2 months" 

- compression 配置文件的压缩格式，可以配置常见的格式 zip、tar、gz、tar.gz 等

 

## 4、loguru字符串输出

最重要的！loguru还提供了字符串格式化输出日志的功能，如下面代码

```python
logger.info('If you are using Python {}, prefer {feature} of course!', 3.6, feature='f-strings')
n1 = "cool"
n2 = [1, 2, 3]
logger.info(f'If you are using Python {n1}, prefer {n2} of course!')

```

运行结果

```python
2020-03-07 16:19:25.363 | INFO     | __main__:<module>:43 - If you are using Python 3.6, prefer f-strings of course!
2020-03-07 16:19:25.364 | INFO     | __main__:<module>:46 - If you are using Python cool, prefer [1, 2, 3] of course!

```

可以看到，只要你会Python字符串格式化输出，这个简直信手拈来！

## 5、loguru封装类，可以直接拿去用

日志输出路径：你的项目路径下的log文件夹下

注意：这个是工具类，需要放在项目路径下的util文件夹之类的，不能直接放项目路径下哈，不然路径会生成错误哦

```python
"""操作日志记录
"""
import time
from loguru import logger
from pathlib import Path

project_path = Path.cwd().parent
log_path = Path(project_path, "log")
t = time.strftime("%Y_%m_%d")


class Loggings:
    __instance = None
    logger.add(f"{log_path}/interface_log_{t}.log", rotation="500MB", encoding="utf-8", enqueue=True,
               retention="10 days")

    def __new__(cls, *args, **kwargs):
        if not cls.__instance:
            cls.__instance = super(Loggings, cls).__new__(cls, *args, **kwargs)

        return cls.__instance

    def info(self, msg):
        return logger.info(msg)

    def debug(self, msg):
        return logger.debug(msg)

    def warning(self, msg):
        return logger.warning(msg)

    def error(self, msg):
        return logger.error(msg)


loggings = Loggings()
if __name__ == '__main__':
    loggings.info("中文test")
    loggings.debug("中文test")
    loggings.warning("中文test")
    loggings.error("中文test")

    logger.info('If you are using Python {}, prefer {feature} of course!', 3.6, feature='f-strings')
    n1 = "cool"
    n2 = [1, 2, 3]
    logger.info(f'If you are using Python {n1}, prefer {n2} of course!')
```

