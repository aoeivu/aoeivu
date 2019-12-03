---
layout: mypost
title: Git Fork flow
categories: [Git]
---

# Fork分支
1. 点击fork按钮  
![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/1.jpg?raw=true)
2. 选择自己的namespace  
![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/2.jpg?raw=true)  
等待fork完成  
![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/3.jpg?raw=true)  
3. 查看自己git的分支  
![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/4.jpg?raw=true)

# 拉取fork分支的内容
1. 新建准备存放git文件的路径  
例如: H:\10_Git\python
2. 进入文件夹右击->点击Git Bash Here 
3. 在弹出的Git窗口输入下面命令初始化该文件夹

    ```
    git init
    ```

    可以看到返回如下内容  
    
    ```
    Initialized empty Git repository in H:/10_Git/python/.git/
    ```
4. 进入自己fork的分支，点击复制  
   ![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/5.jpg?raw=true)
5. 回到Git的DOS窗口输入, clone后为复制的url，耐心等待仓库克隆完成  
   
   ```
   git clone gitlab@192.168.10.95:Flynn.Chen/python.git
   ```
   
   可以看到返回如下内容  

   ```
   $ git clone gitlab@192.168.10.95:Flynn.Chen/python.git
    Cloning into 'python'...
    remote: Enumerating objects: 2558, done.
    remote: Counting objects: 100% (2558/2558), done.
    remote: Compressing objects: 100% (970/970), done.
    remote: Total 2558 (delta 1573), reused 2558 (delta 1573)
    Receiving objects: 100% (2558/2558), 1.06 GiB | 11.08 MiB/s, done.
    Resolving deltas: 100% (1573/1573), done.
    Checking out files: 100% (64/64), done.
   ```

6. 关闭并重新打开git窗口输入如下指令  
    
    ```
    git branch -r
    ```

    看到返回如下内容，如果看不到，多次关闭并重新打开Git DOS  

    ```
    $ git branch -r
    origin/HEAD -> origin/master
    origin/develop
    origin/master
    ```
7. 切换到develop分支准备进行BUG修改  
    
    ```
    git checkout develop  
    ```

    可以看到返回如下内容  

    ```
    $ git checkout develop
    Switched to a new branch 'develop'
    Branch 'develop' set up to track remote branch 'develop' from 'origin'.
    ```
8. 修改任意bug并查看当前分支状态  
   
   ```
   git status
   ```

   可以看到修改(modified)了gitignore文件  

   ```
   $ git status
    On branch develop
    Your branch is up to date with 'origin/develop'.

    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   .gitignore

    no changes added to commit (use "git add" and/or "git commit -a")

   ```
9. 将修改的文件增加到待提交
    
    ```
    git add .gitignore
    ```

10. 再次查看分支状态
    
    ```
    git status
    ```

    看到待提交的变绿

    ```
    $ git status
    On branch develop
    Your branch is up to date with 'origin/develop'.

    Changes to be committed:
    (use "git reset HEAD <file>..." to unstage)

            modified:   .gitignore
    ```
11. 为刚才的修改增加log，test为log内容
    ```
    git commit -m "test"
    ```

    看到如下内容

    ```
    [develop 2334660] test
    1 file changed, 2 insertions(+), 1 deletion(-)
    ```
12. 推送当前的提交到远程develop分支
    
    ```
    git push origin develop
    ```

    git push <本地branch> <远程banch>

13. 至此一次bug修复提交到自己的远程分支就完成了

# merge修改到fork的原分支
1. 进入Git网页版本自己的分支，点击Creat Merge Request  
    ![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/6.jpg?raw=true)

2. 填入合入信息，具体如下
    ![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/7.jpg?raw=true)

3. 上一步Change Branch点击后进入如下界面  
    本次是从自己的develop推送到fork分支的develop，所以如下选择，并点击Compare branches and continue  
    ![fork](https://github.com/aoeivu/aoeivu.github.io/blob/master/posts/2019/12/03/8.jpg?raw=true)
4. 等待自动回到第二步界面，检查信息无误，点击Submit merge request，通知Owner进行对比合并

# fork的远程分支有修改，同步最新的fork的远程分支到自己的本地和远程
1.  添加远程仓库到本地remote分支
```
git remote add upstream gitlab@192.168.10.95:5G-SDX55/Standard.git
```
2. 查看当前仓库的远程分支
```
$ git remote -v
origin  gitlab@192.168.10.95:Flynn.Chen/Standard.git (fetch)
origin  gitlab@192.168.10.95:Flynn.Chen/Standard.git (push)
upstream        gitlab@192.168.10.95:5G-SDX55/Standard.git (fetch)
upstream        gitlab@192.168.10.95:5G-SDX55/Standard.git (push)
```
3. fetch远程分支(更新最新远程分支到本地分支)
```
$ git fetch upstream
remote: Enumerating objects: 129, done.
remote: Counting objects: 100% (116/116), done.
remote: Compressing objects: 100% (46/46), done.
remote: Total 79 (delta 39), reused 62 (delta 22)
Unpacking objects: 100% (79/79), done.
From 192.168.10.95:5G-SDX55/Standard
 * [new branch]      develop        -> upstream/develop
 * [new branch]      master         -> upstream/master
 * [new tag]         20191101_B1.01 -> 20191101_B1.01
```
4. 合并fetch分支到自己master或者dev
```
git merge upstream/develop
```
5. 推送到自己远程仓库
```
git push origin develop
```
