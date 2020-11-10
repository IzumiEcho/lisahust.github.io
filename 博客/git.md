# gitlab笔记

[toc]

![img](http://kmknkk.oss-cn-beijing.aliyuncs.com/image/git.jpg)



### git规范

---

##### git commit命名规范

```
feat：完成一些功能
fix：修复一些问题,如果有jira号，也可以补充上，方便qa进行问题跟进
docs：一些文档修改
style：样式修改
refactor：一些无关功能性的修改，例如重命名
perf：一些优化重构
test：测试相关提交
chore：引入一些第三方库等
```

##### git branch命名规范

```
master——最为稳定功能最为完整的随时可发布的代码；
hotfix——修复线上代码的 bug；
develop——永远是功能最新最全的分支；
feature——某个功能点正在开发阶段；
release——发布定期要上线的功能。
```



### git远程分支本地分支 ｜ 分支管理

---

##### 本地分支管理

```shell
//列出所有分支
git branch -a 

//创建本地分支miya_test
git branch miya_test

//切换分支miya_test
git checkout miya_test

//建立上游分支
git branch --set-upstream-to=origin/feature/pls_20200803_label
```

##### 新建分支

```shell
//在本地新建和master一样的分支
git checkout -b feature/pls_20200804_circle origin/master

//在远程新建和本地同名的分支
git push origin HEAD:feature/pls_20200803_label 

//在本地新建和远程一样的分支
git fetch
git checkout feature/pls_20200804_circle
```

##### 删除分支

```shell
//删除本地分支
git branch -d test  // 删除test分支

//删除远程分支
git push origin --delete feature/pls_user_info_change
```



### git push

---

```shell
git status //查看代码状态变化
git add -A //提交所有修改内容
git commit -m "xxxx" //提交至本地 xxx为提交说明文字
git push //推送到自己的远程仓库 一般直接 commit and push 也可分开两个步骤

//将本地代码推送到远程分支
git push origin HEAD:feature/20200723_pls_modifynote
```



### git分支合并

```shell
git branch --set-upstream-to=origin/master

//清除已commit的部分(不会改变代码)
git reset origin/master
//查看代码状态
git status

//对比origin/master，提交自己改动的代码部分，且提交为一个commit
git add src/main/java/com/xiaohongshu/sns/themis/controller/task/label/  
git commit -m "miya_first_commit" 

//对代码进行合并
git checkout master
//需要显示“已经是最新的”
git pull
git merge miya_test

//merge后检查冲突，若有改动则合并commit
mvn clean package

//合并commit 若有冲突且进行了更改才做这一步，没有也要commit 吧
git add .
git commit --amend

//先推送到自己的fix分支 -f 是代表强制推
git push origin HEAD:yh-online-fix -f

//在gitlab上检测无误后推送
git push



//合并代码前需要rebase
Git rebase -i master
```



### git清除提交

```shell
//通过reset，把HEAD指针指向 origin/master 清除提交，但是没有变代码
git reset origin/master 

//清除所有修改，会删除代码！ 抛弃没有add的代码
git checkout .

//不要用 = = 清除掉没有add的修改
git clean -f
git clean -fd

git checkout feature/20200709_test_pls
```





### fatel:当前分支没有对应的上游分支

git branch --set-upstream-to=origin/master

git push origin HEAD 推送至远程同名分支(若没有则创建)

为推送当前分支并建立与远程上游的跟踪，使用 `git push --set-upstream origin master`



为推送到远程的上游分支，使用 `git push origin HEAD:master`

