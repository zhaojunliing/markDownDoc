## python查看已安装包的版本

### 一、看特定的软件包
pip freeze就不要说了，当你安装1000个包的时候就不会用这种蠢办法。

第一种办法，打开终端/CMD

```shll
pip freeze | findstr numpy
这是windows下的，numpy只是个例子，要查什么自己改
pip freeze | grep numpy
linux下的
```

第二种方法，python里去看。打开python命令行界面。
一般来说包的版本都会用一个.__version__来保存其版本参数

```shell
import networkx as nx
nx.__version__
```

总的来说，推荐第一种方法。
### 二、全部软件包
使用pip可以查看已经安装的Python软件包及其版本：
命令行中:

```shell
1. pip freeze
2. pip list
3. pydoc modules
```

进入python环境:
在交互式解释器中输入

```shell
help("modules")
即可，效果跟在命令行下输入 pydoc modules是一样的
```

还有一种方法:

```shell
import sys
sys.modules.keys()
```


还有一个方法:

```shell
#python -m pydoc -p 1234
pydoc server ready at http://localhost:1234/
```


然后在网页中输入:http://localhost:1234/