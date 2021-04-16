# git和SVN同时管理

### 首先从svn仓库克隆代码

    git svn init https://192.168.30.105/svn/2018_develop/站段干部履职系统/07source/trunk/jeefh_gblz_zd jeefh_gblz_zd
	cd jeefh_gblz_zd/
	git svn fetch -r HEAD

### 添加git仓库地址

    cd demo
    git remote add origin http://192.168.10.199:81/zhaojunli/jeefh_gblz_zd

### 获取git仓库的分支

    git fetch origin master	#获取git仓库的master分支
    git fetch origin 1.0   	#获取git仓库的1.0分支
    git fetch origin   		#获取所有git分支

### 此时本地git的分支情况

 	demo git:(master) git branch -a
	* master              #默认对应svn
  	remotes/git-svn     #svn分支
  	remotes/git/1.0     #远程git的1.0分支
  	remotes/git/master  #远程git的master分支

### 创建新的分支并强行merge分支

### 提交分支到远程

### 更新并提交SVN信息到远程

### 将git代码合并到svn分支
	
	git merge git-master

### 更新并提交

    git svn rebase
    git svn dcommit



#### 附加命令
	
- git branch -a 查看分支
- git diff		查看不同
- git remote add origin https://github.com/tielemao/TielemaoMarkdown  添加远程仓库
- git svn rebase 与远端同步
- git svn dcommit 将修改提交到SVN
- git status 查看状态
- git branch new-branch_name  新建分支 
- git checkout -b new_branch_name  新建分支并跳转到分支
- git branch -d branch_name 删除分支 (删除分支时，不可处于当前分支)
- git log
- git checkout branch_name  切换分支
- git remote -v  查看远程仓库
- git stash  把本地未提交的文件缓存
- git reset –hard HEAD  放弃本地修改
- git pull origin master --allow-unrelated-histories  合并两个没有关联的项目


### git修改远程仓库地址
1. 修改命令

	```
	git remte origin set-url URL
	``` 
2. 先删后加
	```
	git remote rm origin 
	git remote add origin git@github.com:Liutos/foobar.git
	```


git svn init --stdlayout https://192.168.30.105/svn/2018_develop/站段干部履职系统/07source/trunk/jeefh_gblz_zd --prefix=svn/  



https://blog.csdn.net/thanklife/article/details/80162938