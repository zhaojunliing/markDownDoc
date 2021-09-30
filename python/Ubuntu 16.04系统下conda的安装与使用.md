# Ubuntu 16.04系统下conda的安装与使用

> conda是一个开源的软件包管理系统和环境管理系统，用于安装多个版本的软件包及其依赖关系，并在它们之间轻松切换。 conda是为 Python 程序创建的，适用于 Linux，OS X 和Windows，也可以打包和分发其他软件。conda分为anaconda和miniconda，anaconda是包含一些常用包的版本，miniconda则是精简版，只包含 conda 和其依赖，所以推荐使用miniconda。本文以Ubuntu 16.04 LTS系统为例，介绍miniconda的安装与使用。

1、用wget命令下载

```shell
wget -c https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

2、执行安装

```shell
chmod 777 Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh
```

安装过程中，提示阅读用户许可协议，需要按“Enter”继续，并输入 “yes”继续安装，并列出conda安装的具体路径，这个路径保存一下。

最后询问是否初始化Miniconda3时，选“no”

3、验证conda是否安装成功

```shell
conda
```

4、添加清华大学的镜像源

```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --set show_channel_urls yes 
conda config --get channels
```

镜像源删除重新添加

```shell
#conda config --remove channels 'https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/'
#conda config --remove channels 'https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/'
#conda config --remove channels 'https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/'
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
```



5、环境管理命令

```shell
(base) root@fh-ai:~# conda env list
# conda environments:
#
                         /usr/local/minicoda3
base                  *  /usr/local/miniconda3
3.6.7                    /usr/local/miniconda3/envs/3.6.7
result2                  /usr/local/miniconda3/envs/result2
result3                  /usr/local/miniconda3/envs/result3
```

使用命令“conda create -n 环境名称 python=版本号”创建环境，这里创建了名称为3.6.7（名称不是很好）的python版本号为3.6.7的虚拟环境，稍微等待，过程中输入“y”。

```shell
conda create -n result3 python=3.7.7
```

激活环境，默认处于base环境，进入其他环境需要使用conda activate手动切换

```shell
(base) root@fh-ai:~# conda activate result3
(result3) root@fh-ai:~# 
```

退出当前环境，使用conda deactivate，默认回到base 环境

```shell
(result3) root@fh-ai:~# conda deactivate
(base) root@fh-ai:~# 
```

