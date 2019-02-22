# git培训

## 1.团队工作流(两个团队成员及以上协作时)

![git working flow](https://blog.bingo.ren/images/blog/40/git-workflow-release-cycle-4maintenance.png)

### 1.分支说明

* Master(用于正式线上环境)
* Hotfix(用于线上bug修复使用的分支)
* Release(目前暂时作为测试环境分支使用,注意：开发过程如果需要频繁给到项目经理或者测试人员时,不要频率迭代release分支,在develop分支更新然后使用局域网地址)
* Develop(开发环境使用的分支,在完成每一个特性功能分支开发后需要合并到该分支,该分支会设置为保护分支,需要提交merge request申请组长去合并)
* Featrue(功能性分支,实际上命名不要以Featrue1,Featrue2这样去命名，应该用Featrue-login,Featrue-personal去命名)


### 2.当项目只有一个成员开发时,上面的流程有所简化,dev会设置成开发性分支，成员完成某个模块功能后直接推送代码到dev分支即可


### 3.为方便组长定时对成员代码进行code review,请务必频繁提交代码到远程仓库，最好保持每天一次

## 2.git基本操作

 http://www.runoob.com/git/git-tutorial.html


## 3.git 远程仓库操作(核心)

关于远程仓库操作请仔细阅读完以下链接内容

http://www.ruanyifeng.com/blog/2014/06/git_remote.html

