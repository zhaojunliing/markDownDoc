介绍如何将centos7的Python由2升级到3

## 工具/原料

- centos7
- Python-3.6.5.tgz

## 方法/步骤

1. 

   目前，centos7默认安装的是Python2.7

   执行python -V可以查看当前centos中安装的Python的版本

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/5e4e9c2b74ee1c326b93fa9907f1d8a727334b20.jpg)

2. 

   下载Python3.x，进入官网下载

   如图，点击图示第一个，下载.tgz压缩文件

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/ff5c88d8181961202b41a7db70f202b374d7df21.jpg)

3. 

   执行 tar -zvxf Python-3.6.5.tgz解压安装包

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/979906196120a7cd81e2504789b375d7987bdc21.jpg)

4. 

   进入解压后的文件夹：cd Python-3.6.5

   执行 ./configure

   检查系统是否有合适的编译器编译源代码及是否具备正确的依赖关系

   如图执行后报了错误信息，缺失GUN C编译器

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/19587f20a7cd0c6e757ea906fed7997bbaf4dd21.jpg)

5. 

   执行yum install gcc安装

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/6061b9cd0c6efbf28d3fde62127bbbf4db58da21.jpg)

6. 

   安装完成之后，再次执行./configure，结果如图

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/a68c126efbf202b3e55b32ce30f4da5873dadb21.jpg)

7. 

   执行make编译源码，执行过程中会有大量输出

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/0d2fe5f202b375d708f71041515872dadf49d821.jpg)

8. 

   执行make isntall 安装

   安装过程中报错zipimport.ZipImportError: can't decompress data; zlib not available

   解决办法：执行yum -y install zlib* 安装所需依赖

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/fab31cb375d7997b297871edf9dade49600fd921.jpg)

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/03f26bd7997bbbf449d4d96f5549610f8a56d621.jpg)

9. 

   再次执行make install，也会有大量输出，如图安装成功

   ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/7496877bbbf4da58e65675fcea0f8b56ac04d721.jpg)

10. 

    备份原有配置，设置python默认版本号为3.x

    mv /usr/bin/python /usr/bin/python.bak

    再执行：

    ln -s /usr/local/bin/python3 /usr/bin/python

    最后，执行python -V验证，如图版本变成了3.x

    ![CentOS 7如何将Python 2.X.X升级Python 3.X.X](assets/983aa5f4da5872da4bc5caba0056ad042f68d421.jpg)