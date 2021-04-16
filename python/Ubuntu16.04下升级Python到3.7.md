# Ubuntu16.04下升级Python到3.7

> 开发一个Python的系统，需要安装Python3.6以上的版本，由于使用的操作系统是Ubuntu16.04，默认带的Python是2.7.12和3.5，不满足需求，所以需要升级Python。

## 1、增加ppa仓库：

```shell
sudo add-apt-repository ppa:deadsnakes/ppa	
```

 ppa:jonathonf/python-3.7 这个不能用了

## 2、升级apt索引，更新python

```shell
sudo apt-get update

sudo apt-get install python3.7
```

## 3、更换系统默认的软链命令Python3到新的Python3.7

```shell
sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 1

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.7 2
```

上面两句指令就是修改了系统默认的/usr/bin/python3 的软链指向，指向了两个位置，最后面的1和2是优先级。

## 4、切换Python3的指向

update-alternatives命令可以修改系统默认命令的软链指向，通过以下命令，可以切换Python3的指向

```shell
sudo update-alternatives --config python3　　　　　　
```

## 5、pyinstaller打包问题

会遇到无法打包问题

* On Debian/Ubuntu, you would need to install Python development packages
*  apt-get install python3-dev

* apt-get install python-dev

```shell
apt autoremove python-dev
apt autoremove python3-dev
apt-get install python3.7-dev
```

卸载原来的，安装对应版本的dev即可