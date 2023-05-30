# Gradle 引用本地 Jar 包

## 1、在项目根目录创建名为 lib 的目录，并将本地 Jar 包复制进去。
如下图所示，引入 CacheDB 本地 jar 包：

![img](F:\github\markDownDoc\gradle\assets\8d41bd3aea074e318a756c18a434d7db.jpeg)

## 2、将 Jar 包引入项目中去

### 方式 1：通过 dependencies 引入

```shell
dependencies {
    // 依赖lib目录下的某个jar文件
    implementation files('lib/xxx.jar')


    // 依赖lib目录下的所有以.jar结尾的文件
    implementation fileTree(dir: 'lib', includes: ['*.jar'])


    // 依赖lib目录下的除了xxx.jar以外的所有以.jar结尾的文件
    implementation fileTree(dir: 'lib', excludes: ['xxx.jar'], includes: ['*.jar'])
}
```

如下图所示，在 Gradle 的 compileClassPath 里面可以看到 directory 'lib'

![img](F:\github\markDownDoc\gradle\assets\c04740b293ba41a2b98e4c4108820575.png)

### 方式 2：通过 repositories 引用
 使用此方法引入的本地 jar 包，不会在 External Libraries 中显示，但可以使用

```
repositories {
	flatDir(dirs: "lib")
    mavenCentral()
}
```

