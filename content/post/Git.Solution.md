
// TODO
# 描述


# 概要

## 相关概念


FETCH_HEAD

commit-id

## 文件结构


# 场景

## 下载
### 沿用线上内容
git clone git@github.com:yqjdcyy/Hello_Git.git

### 使用本地内容
git init
git remote add origin git@github.com:yqjdcyy/Hello_Git.git
git remote -v


## 拉取代码
### Fetch 形式
git fetch origin master:temp
git diff temp
git merge temp
git branch -d temp

### Pull 形式
git pull origin master


## 提交代码
git add -A
git commit -m "commit.content"
git push origin master


## 切换分支

### 以当前分支内容为基础
git checkout -b branch-new

### 拉取线上分支
git checkout -b dev origin/dev
    适用于本地没 dev 分支，直接拉取线上 dev 分支内容并切换过去
    若使用上者，刚发现原分支的内容将被合并过来

## 提交分支
git checkout -b prod
git push origin prod


## 查看日志
### 提交提交日志

git log
git log --pretty=oneline

### 操作操作日志
git reflog

### 查看代码提交人
git blame <file>


## 回滚
### 撤消本地未提交文件修改

git checkout -- <file>...
    针对现有文件的修改

### 撤消暂存区修改

git reset HEAD <file>...
    针对 git add 的文件

### 本地提交回退

git reset --hard <commit-id>
    指定回退到的节点
git reset --hard HEAD~n
    针对本地提交，但未推送至远程分支的情况
    该提交内容直接丢弃
    可结合 git reflog 回滚本地至本处操作节点
git reset --soft HEAD~n
    回退版本，并将回退的内容置于工作区
git reset --soft HEAD~n
    仅回退 commit 记录，不回退内容
    
### 拉取代码回退

git reset --hard ORIG_HEAD
    针对 git pull 合并冲突无法立即修改，而本地又需向前迭代情况

### 线上版本回退
git revert HEAD~n
    回滚倒数第 n+1 个提交记录
git revert <commit-id>
    指定回退指定节点
    新的提交，内容与回退内容相反
        会影响到分支再次合并，该部分内容亦无法重新出现
git revert <old-commit-id>^..<new-commit-id>
    回滚指定 commit 范围内的记录，前旧后新
    命令执行后，会针对每个 commit 的回滚让你标记相应的 commit 内容
git revert <commit-id> -m [1|2]
    针对 merge 操作的回滚
    merge <2> to <1>，其中-m 后面的 <1|2> 用于指定所要保留的分支
        如 merge dev to master， -m 1 则表示要保留 master

## 暂存

git stash
git stash pop


## Pull Request


# 参考
- [git - 简易指南](http://www.bootcss.com/p/git-guide/)
- [git中fetch和pull的区别](https://www.jianshu.com/p/d265f7763a3a)
- [git reset 应用场景示例](https://blog.csdn.net/qq_27258799/article/details/53504451)
- [Git 获取远程分支](https://www.cnblogs.com/hanxianlong/archive/2012/09/10/2678659.html)
- [git中reset与revert的区别](https://www.jianshu.com/p/0e1fe709dd97)
- [revert多个提交](https://blog.csdn.net/hongchangfirst/article/details/80986597)
- [git-revert](https://mirrors.edge.kernel.org/pub/software/scm/git/docs/git-revert.html)
- [git revert merge 的commit](https://www.cnblogs.com/520yang/articles/6732687.html)
- []()
- []()
- []()
- []()
- []()




