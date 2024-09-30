# Oracle em使用http

### 1. 设置[oracle](https://so.csdn.net/so/search?q=oracle&spm=1001.2101.3001.7020)唯一名称

执行emctl时需要设置一个唯一的名称 否则提示 “Environment variable ORACLE_UNQNAME not defined. Please set ORACLE_UNQNAME to [database](https://so.csdn.net/so/search?q=database&spm=1001.2101.3001.7020) unique name. ”中文意思为“未定义环境变量ORACLE_UNQNAME。 请将ORACLE_UNQNAME设置为数据库唯一名称/服务名称。”

set oracle_unqname=orcl

### 2.可以将Oracle EM 网站由https 转换为http 模式

解决办法：

1、采用google 浏览器，Chrome
在windows的安全机制中，对https的访问安全要求较高，IE无法使用，安装Firefox或者chrome浏览器，推荐Chrome；
那么Firefox的做法是将“https://localhost:1158/em”添加到安全选项的例外中；
chrome，不做作保设置，只给提示，不直接阻止，你只要继续浏览就可以了。

2、可以将Oracle EM 网站由https 转换为http 模式,操作如下：

https 变成 http
执行 $ emctl unsecure dbconsole
然后 $ emctl start dbconsole

http 变成 https
执行 $ emctl secure dbconsole
然后 $ emctl start dbconsole