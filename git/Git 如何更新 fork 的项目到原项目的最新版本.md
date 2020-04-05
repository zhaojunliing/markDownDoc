### git 如何更新 fork 的项目到原项目的最新版本

在 github 上 fork 了一个项目之后，如何使自己 fork 的项目和原先作者的项目分支保持同步呢，下面我使用 hutool 的项目做示范，手摸手示范如何使 fork 的项目与原项目分支保持同步。

1. **查看远程的版本库地址** 

   ```bash
   $ git remote -v 
   origin https://github.com/xkcoding/hutool.git (fetch) 
   origin https://github.com/xkcoding/hutool.git (push) 
   ```

2. **添加原项目 git 地址到本地版本库** 

   ```bash
   $ git remote add upstream https://github.com/looly/hutool.git 
   ```

3. **检查版本库是否添加成功** 

   ```bash
   $ git remote -v
   origin  https://github.com/xkcoding/hutool.git (fetch)
   origin  https://github.com/xkcoding/hutool.git (push)
   upstream        https://github.com/looly/hutool.git (fetch)
   upstream        https://github.com/looly/hutool.git (push)
   ```

4. **原项目更新内容同步到本地** 

   ```bash
   $ git fetch upstream                             
   remote: Counting objects: 360, done.
   remote: Compressing objects: 100% (148/148), done.
   remote: Total 360 (delta 158), reused 339 (delta 137), pack-reused 0
   Receiving objects: 100% (360/360), 87.95 KiB | 45.00 KiB/s, done.
   Resolving deltas: 100% (158/158), completed with 66 local objects.
   From https://github.com/looly/hutool
    * [new branch]        v4-dev     -> upstream/v4-dev
    * [new branch]        v4-master  -> upstream/v4-master
   ```

5. **查看本地分支** 

   ```bash
   $ git branch -a 
   * v4-master 
   remotes/origin/HEAD -> origin/v4-master 
   remotes/origin/v4-dev 
   remotes/origin/v4-master 
   remotes/upstream/v4-dev 
   remotes/upstream/v4-master 
   ```

6. **同步更新内容到本地对应分支** 

   ```bash
   $ git merge upstream/v4-master 
   ```

7. **提交更新内容到 fork 地址** 

   ```bash
   git push 
   ```
