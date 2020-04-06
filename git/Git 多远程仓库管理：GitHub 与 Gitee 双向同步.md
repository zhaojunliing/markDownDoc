# Git 多远程仓库管理：GitHub 与 Gitee 双向同步

> Github下载文件慢，有的同事又不会优化网络，打算使用 Gi­tee 做个镜像仓库，于是就有了 GitHub 和 Gi­tee 双向同步的需求。

## 主仓库和镜像仓库

GitHub 为主仓库，Gi­tee 为镜像仓库，所以需要实现从 GitHub 拉取，本地修改提交后同时推送到两个平台。

本地有一个仓库已经关联到了 GitHub 远程仓库，名为 `origin`。

```shell
$ git remote -v
origin  git@github.com:P3TERX/img.git (fetch)
origin  git@github.com:P3TERX/img.git (push)
```

把 Gi­tee 远程仓库的链接添加到 `origin`。

```shell
$ git remote set-url --add origin git@gitee.com:P3TERX/img.git
```

查看远程仓库信息：

```shell
$ git remote -v
origin  git@github.com:P3TERX/img.git (fetch)
origin  git@github.com:P3TERX/img.git (push)
origin  git@gitee.com:P3TERX/img.git (push)
```

此时使用 `git pull` 可以从 GitHub 上拉取，使用 `git push` 可以同时推送到 GitHub 和 Gi­tee 。

## 分别拉取和推送

按照下面的方法设置后使用场景会灵活很多，比如可以从 GitHub 拉取，然后推送到 Gi­tee ，也可以反过来，真正做到双向同步。

添加 GitHub 远程仓库并命名为 `github`。

```shell
$ git remote add github git@github.com:P3TERX/img.git
```

添加 Gi­tee 远程仓库并命名为 `gitee`。

```shell
$ git remote add gitee git@gitee.com:P3TERX/img.git
```

查看远程仓库信息：

```shell
$ git remote -v
origin  git@github.com:P3TERX/img.git (fetch)
origin  git@github.com:P3TERX/img.git (push)
origin  git@gitee.com:P3TERX/img.git (push)
gitee   git@gitee.com:P3TERX/img.git (fetch)
gitee   git@gitee.com:P3TERX/img.git (push)
github  git@github.com:P3TERX/img.git (fetch)
github  git@github.com:P3TERX/img.git (push)
```

此时使用以下命令就可以分别从两个远程仓库拉取和推送到两个远程仓库。

```shell
$ git pull github master
$ git pull gitee master
$ git push github master
$ git push gitee master
```

