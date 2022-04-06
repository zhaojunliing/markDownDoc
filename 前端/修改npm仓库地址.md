# 修改npm仓库地址

### 解决方案

npm config set registry http://registry.npm.taobao.org/

将npm默认设置为淘宝镜像地址

### 发布包

当你想发布自己的包时，需要将地址修改回来

npm config set registry https://registry.npmjs.org/

```
//设置淘宝源
npm config set registry https://registry.npm.taobao.org

//设置公司的源
npm config set registry http://127.0.0.1:4873

//查看源，可以看到设置过的所有的源
npm config get registry

//本次从淘宝仓库源下载，临时使用
npm --registry=https://registry.npm.taobao.org install

```

### 使用cnpm命令代替npm

```
// 安装cnpm命令,不会改变npm的源
npm install -g cnpm --registry=https://registry.npm.taobao.org

//使用
cnpm install
```



