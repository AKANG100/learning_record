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

* git log
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

* 本地做了提交

* git push origin master         // 推送最新更改

* ```
  git remote -v
  git remote rm <file>   //删除
  ```



##### (2)从远程库克隆

* git clone git@github.com:AKANG100/gitskills.git



---

---



## 11.17

#### 1.分支管理

