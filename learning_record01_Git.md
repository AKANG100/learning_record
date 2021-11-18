## 11.16

[廖雪峰教程](https://www.liaoxuefeng.com/wiki/896043488029600)

#### 1.Git的使用

##### (1)创建版本库，添加文件到Git仓库

* mkdir learngit
* cd learngit
* pwd
* git init
* touch readme.txt
* git add readme.txt
* git commit -m "wrote a read file"



##### （2）掌握工作区状态，查看修改内容

* git status
* git diff
* git add readme.txt
* git commit -m “append GPL"
* git status



##### (3)版本回退

* git log      //查看历史记录
* git reflog
* git reset --hard commit_id



#####  （4）工作区和暂存区



##### （5）管理修改



##### (6)撤销修改

* git restore <file>

* git checkout --<file>         效果一样



#####  (7)删除文件

* git rm <file>
* git commit -m <remove file>



#### 2.远程仓库的使用

##### (1)添加远程仓库

* ```
  git remote add origin git@github.com:AKANG100/learngit.git  // 关联远程库
  ```

* 本地做了提交

* git push origin master         // 推送最新更改

* ```
  git remote -v
  git remote rm <file>   //删除
  ```



##### (2)从远程库克隆

* git clone git@github.com:HNUYueLuRM/RoboMaster2021-HNU-YueLuRm-Vision



---

---



## 11.17

#### 1.分支管理

##### （1）创建与合并分支

* git branch	//查看分支
* git switch -c dev	//创建并切换到新的dev分支
* git switch master	//切换到已有的master分支
* git merge dev	//把dev分支的工作成果合并到master分支上
* git branch -d dev     //删除dev分支



##### (2)解决冲突

* 当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

  解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

  用`git log --graph`命令可以看到分支合并图。



##### (3)分支管理策略

- ```
  git merge --no-ff -m "merge with no-ff" dev
  //准备合并dev分支，请注意--no-ff参数，表示禁用Fast forward
  ```

* 分支策略



##### (4)bug分支

* git stash
* git checkout master
* git checkout -b issue-101
* gti add readme.txt
* git commit -m"fix bug 101"
* git switch master
* git merge  --no-ff -m "merged bug fix 101" issue-101
* git switch dev
* gitn status
* git stash list
* git stash pop
* gut stash list 
* git stash apply stash@{0}



##### （5）feature分支



##### (6)多人协作

* git remote		//查看远程库的信息
* git remote -v
* git push origin master
* git push origin dev      //推送其他分支



##### （7）Rebase



#### 2.标签管理

##### （1）创建标签

* git tag <tagname>      // 默认为HEAD，可以指定一个commit id

* git tag
* git tag <tagname>



##### (2)标签操作

- `git push origin <tagname>`可以推送一个本地标签；
- `git push origin --tags`可以推送全部未推送过的本地标签；
- `git tag -d <tagname>`可以删除一个本地标签；
- `git push origin :refs/tags/<tagname>`可以删除一个远程标签。



#### 3.使用Github

#### 4.使用Gitee
