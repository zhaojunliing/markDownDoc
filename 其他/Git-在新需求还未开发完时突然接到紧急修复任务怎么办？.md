# Git-在新需求还未开发完时突然接到紧急修复任务怎么办?
id: git-stash
title: Git-在新需求还未开发完时突然接到紧急修复任务怎么办
date: 2017-08-16 23:04:15
tags: [Git]
cover: http://7xnocv.com1.z0.glb.clouddn.com/20170820231931_oPjVXR_uyTj5fx62rjA8IfUK2xI.jpeg

categories: 技术
---

## 关键字
stash 储藏，保存当前的工作进度
pull 从服务器更新代码
push 将本地封存的修改推送到服务器
commit 提交修改到本地git（注意与SVN不一样）
<!-- more -->

## 何谓储藏
具体请看这篇文章[《Git 工具 - 储藏（Stashing）》](https://git-scm.com/book/zh/v1/Git-工具-储藏（Stashing）)

不理解的话按我们开发过程中遇到的场景来讲一下

## 在新需求还未开发完时突然接到紧急修复任务

我们在开发新需求（未开发完）时，突然有紧急bug要修复。此时，我们面临的问题有3个。

1. 如何存放未完成的新需求代码？
2. 如何回到bug代码现场？
3. 如何在修复完bug后接着开发之前未完成的新需求？

幸运的是Git解决这些问题快速且简单。大致只需3步：
1. stash 储藏当前工作进度，取名为tmp（名字任意取，便于在恢复时区分）
  这时新需求的未完成代码已经储藏在了本地Git，并且工程目录下的代码恢复到了你上一次commit的状态，从现在开始修改代码不会影响新需求的代码。（Safari看不到动图，用Chrome吧）

  ![](http://7xnocv.com1.z0.glb.clouddn.com/git-stash-01.gif)

2. 修复bug并将代码push到服务器，在分支上跟我们平时开发提交代码一样，修复完bug测试通过后将代码push到服务。

3. 恢复之前储藏的工作进度，继续开发未完成的新需求（Safari看不到动图，用Chrome吧）

   ![](http://7xnocv.com1.z0.glb.clouddn.com/git-stash-02.gif)

### 注意事项
Apply Stash之后，该Stash依旧留在本地列表中。若不再需要该Stash则需手动drop。你也可以勾选上 “Pop stash” 选项，勾选上后再Apply Stash则会在恢复完之后将这个Stash给Drop掉。这样可以避免本地Stash过多。**drop时一定别删错了，否则将丢失修改的代码**

![](http://7xnocv.com1.z0.glb.clouddn.com/20170820230315_kBevmw_Screenshot.jpeg)

当你在开发新需求和修复紧急bug修改了同一个文件，可能在恢复工作进度时会遇到冲突需要合并。
![](http://7xnocv.com1.z0.glb.clouddn.com/20170820230330_anjAIQ_Screenshot.jpeg)

## 记住
1. 除了第2步（修复bug并将代码push到服务器），其余步骤的操作均存于本地。 当你储藏工作进度的时候，所有这一切都只发生在你本地的 Git 版本库中 —— 没有与服务器发生交互。
2. stash不要存得过多，否则会连自己都搞不清楚存的那是些什么。
3. stash只是临时存放工作进度的一种快捷方法，如果新需求开发周期很长则需要新建分支。

## 分支
看看分支是如何使用的：[《Git 分支 - 何谓分支》](https://git-scm.com/book/zh/v1/Git-分支-何谓分支)、[Git 分支 - 分支的新建与合并](https://git-scm.com/book/zh/v1/Git-分支-分支的新建与合并)、[Git 分支 - 利用分支进行开发的工作流程](https://git-scm.com/book/zh/v1/Git-分支-利用分支进行开发的工作流程)
