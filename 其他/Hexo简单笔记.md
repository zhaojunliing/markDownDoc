# Hexo简单笔记
id: hexo-guide
title: Hexo简单笔记
date: 2014-01-11 09:19:06
tags: [Github,Hexo]
---
## Hexo配置
_config.yml 配置文件，respsitory配置为自己的仓库地址，注意type为git

    deploy:
      type: git
      repository: https://github.com/funxiong/fun90.github.io.git
      branch: master 

修改文章访问地址:

    permalink: :year/:month/:day/:id/


source 博客数据源文件，其中的_post目录即是文章源文件(.md文件)

themes 主题存放目录，推荐主题：Jacman([如何使用 Jacman 主题](http://wuchong.me/jacman/2014/11/20/how-to-use-jacman/))

<!-- more -->

## 新建文章

执行new命令，生成指定名称的文章至hexo\source\_posts\postName.md。、

    hexo new "postName"

或者

在source\_posts目录下新建.md的文件，文件内容：

    title: postName #文章页面上的显示名称，可以任意修改，不会出现在URL中
    date: 2013-12-02 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
    categories: #文章分类目录，可以为空，注意:后面有个空格
    tags: #文章标签，可空，多标签请用格式[tag1,tag2,tag3]
    ----

    正文

## 生成页面
执行generate命令，生成静态页面

    hexo generate

或者简写

    hexo g

## 预览
执行server命令，浏览器输入http://localhost:4000 预览文章

    hexo server

或者简写

    hexo s

## 发布
执行deploy命令，发布到Github服务器(注意把_config.yml的发布方式及地址配好)

    hexo deploy

或者简写

    hexo d

## 相关教程
[Markdown语法教程](http://wowubuntu.com/markdown)