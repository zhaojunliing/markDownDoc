# SVN项目迁移到GIT并保留commit记录

> 迁移一个SVN项目到公司内部GitLab上，本文记录操作过程

### 1. 编辑userinfo.txt

格式为svn_username=git_username<user_email>，提交记录里出现的所有svn_user都要有对应git_user

```shell
svn_user1=svn_user1<svn_user1@xxx.com>
svn_user2=svn_user2<svn_user2@xxx.com>
```

### 2. 从svn拉取代码

配置git信息，以前配置过的可以忽略：

```shell
git config --global user.name "YOUR_USERNAME"
git config --global user.email "YOUR_EMAIL"
```

查看配置:

```shell
git config --list
```

拉取代码（项目越大，花费时间越多）

```shell
git svn clone -r 17155:HEAD svn_url --no-metadata -A userinfo_path local_path
```

- -r 17155:HEAD 代表从svn的282506版本到最新版本的提交记录，避免从第一个版本拉取数据量过大，操作耗时高。
- 如果需要拉取所有记录，可去除 -r 17155:HEAD
- svn_url 你的svn项目地址。
- –no-metadata 防止git拉取无用的SVN附加信息
- local_path 为git clone下来的svn目录，会自动新建
- -A userinfo_path 你的userinfo.txt路径

### 3. 提交代码到git

添加远程仓库地址：

```shell
cd localpath
git remote add origin https://ip:port/xxx/xxx.git
```


查看关联情况:

```shell
git remote -v
```

如果关联有误，可以删除关联

```shell
git remote rm origin
```

### 4. push代码到远端
```shell
git push origin master
```