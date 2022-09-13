## Python 将私有包自动上传Nexus私服

公司内部的一些包需要私有化管理，所以内部搭建了自己的私服，一般情况会采用PyPI或者nexus 搭建，搭建成功后包需要发布到私服；

1、用户根目录（如：C:\Users\Administrator）下新建.pypirc文件，添加如下配置

```
[distutils]
index-servers = 
    nexus

[nexus]
repository: http://192.168.10.161:8880/nexus/repository/pypi-group/
username: 账号
password: 密码
```

```
说明：
index-servers ：定义私服地址名称，需要与仓库节点名称对应起来
[nexus]：定义仓库信息
    repository: 仓库地址（仓库的配置中获取）
    username: 登录账号
    password: 登录密码
```

2、项目发布主要是两步，打包、发布

i.打包

```
python setup.py sdist
```

ii.发布

- 安装python的twine包

```
pip install twine
```

- 发布

```
twine upload -r [nexus] [发布文件路径]

twine upload --repository-url  仓库地址 -u 用户名 -p 密码  pypi包所在的路径

```

iii.打包并发布

```
python setup.py sdist upload -r [nexus]

python setup.py sdist upload -r nexus
```

说明：

nexus 需要与.pypirc配置的节点名称对应起来