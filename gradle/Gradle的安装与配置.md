# 1. Gradle简介

　　Gradle是源于Apache Ant和Apache Maven概念的项目自动化构建开源工具，它使用一种基于Groovy的特定领域语言(DSL)来声明项目设置，抛弃了基于XML的各种繁琐配置面向Java应用为主。当前其支持的语言暂时有Java、Groovy、Kotlin和Scala。

　　Gradle是一个基于JVM的构建工具，是一款通用灵活的构建工具，支持maven， Ivy仓库，支持传递性依赖管理，而不需要远程仓库或者是pom.xml和ivy.xml配置文件，基于Groovy，build脚本使用Groovy编写。

　　IDEA 2017.3以上版本支持。

 

# 2. 下载

　　官方网站：https://gradle.org/install/#manually

　　提供了两种下载方式，Binary-only是只下载二进制源码，Complete, with docs and sources是下载源码和文档。如果有阅读文档的需求可以下载第二个，没有需要的下载Binary-only即可。

 

# 3. 安装

　　解压安装包到想安装到的目录。

 

# 4. 配置

## 　　(1) 配置环境变量

　　　　1) 新建变量

　　　　　　变量名：GRADLE_HOME

　　　　　　变量值：解压到的目录

　　　　　　![img](https://img2018.cnblogs.com/blog/1463514/201909/1463514-20190904133702208-490021808.png)

　　　　2) 新建变量

　　　　　　变量名：GRADLE_USER_HOME

　　　　　　变量值：自定义Gradle仓库目录或者Maven的仓库目录

　　　　　　![img](https://img2018.cnblogs.com/i-beta/1463514/201911/1463514-20191107112830439-540725559.png)

　　　　3) 添加变量

　　　　　　变量名：Path

　　　　　　变量值：%GRADLE_HOME%\bin;

　　　　　　![img](https://img2018.cnblogs.com/blog/1463514/201909/1463514-20190904134044671-1441312871.png)

 

## 　　(2) 配置Gradle仓库源

　　　　在Gradle安装目录下的 init.d 文件夹下，新建一个 init.gradle 文件，里面填写以下配置。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
allprojects {
    repositories {
        maven { url 'file:///C:/Java/maven_repository'}
        mavenLocal()
        maven { name "Alibaba" ; url "https://maven.aliyun.com/repository/public" }
        maven { name "Bstek" ; url "http://nexus.bsdn.org/content/groups/public/" }
        mavenCentral()
    }

    buildscript { 
        repositories { 
            maven { name "Alibaba" ; url 'https://maven.aliyun.com/repository/public' }
            maven { name "Bstek" ; url 'http://nexus.bsdn.org/content/groups/public/' }
            maven { name "M2" ; url 'https://plugins.gradle.org/m2/' }
        }
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

　　　　　repositories 中写的是获取 jar 包的顺序。先是本地的 Maven 仓库路径；接着的 mavenLocal() 是获取 Maven 本地仓库的路径，应该是和第一条一样，但是不冲突；第三条和第四条是从国内和国外的网络上仓库获取；最后的 mavenCentral() 是从Apache提供的中央仓库获取 jar 包。

 

## 　　(3) 配置到IDEA

　　　　在IDEA的Setting里打开"Build, Execution, Deployment"-"Build Tools"-"Gradle"。

　　　　勾选 Use local Gradle distribution，在 Gradle home 中选择安装的Gradle的路径。

　　　　如果在变量和配置文件中设置了Gradle的仓库路径，在 Service directory path 中就会自动填写地址，如果想改的话可以手动修改。

![img](https://img2018.cnblogs.com/blog/1463514/201909/1463514-20190904143044613-183079054.png)