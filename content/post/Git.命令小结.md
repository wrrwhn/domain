+++
date = "2016-12-11T22:07:46+08:00"
title = "Git.命令小结"
draft = false
tags = ["整理","Git"]
share = true
+++


[TOC]

## 查看
### 分支状态
- git status
- git branch
    - 分支列表
    - --merged：查看哪些分支被合并到当前分支
    - --no-merged：查看哪些分支未并合并到当前分支
### 日志
- git log
    - --pretty=oneline：单行显示
    - --graph：图形分支
### 标签
- git tag
    - `<>`：查看当前分支标签
    - -l 'v1.4*'：查看所有匹配该正则的标签信息
### 文件提交明细
- git blame `<fileName>`：查看文件每行的提交 hash / author/ time

## 提交
### 首次提交
- git init： 将当前目录转换为 Git 仓库
- git push -u origin `<branch>`
###新增提交
- git add -A
- git commit -m "commit content"
- git push origin `<branch>`
### 修改提交
- git commit -a -m "commit content"
- git push origin `<branch>`
### 强制提交
- git push -f origin `<branch>`
### 标签
- 单个
    - git push origin `<tagname>`
- 所有
    - git push --tags

## 创建
### 分支
- checkout
    - git checkout -b `<branch>` `<origin/ dev>`： 创建分支 [关联远程分支 dev ]
- branch
    - git branch `<branch>`：创建分支
    - git checkout `<branch>`： 切换分支
### 标签
- git tag
    - `<tagname>`：当前版本上创建标签（轻量标签）
    - `<tagname>` `<commit id>`：针对 `<commit id>` 历史提交打上标签
    - -a `<tagname>` -m "`<more info>`"：创建附注标签

## 切换
### 分支
- git checkout `<branch>`
### 标签
- git checkout `<tagname>`

## 关联
### 关联远程分支
- set
    - git branch --set-upstream-to=origin/dev
    - git branch -u origin/dev
        - 将当前分支的 upstream 更新为 origin/dev 分支
- push & set
    - git push -u origin master
        - 将 master 分支推送至 origin/master 分支
        - 更新本地 master 分支的 upstream 为 origin/master
- set other
    - git push -u origin/dev dev
        - 将本地 dev 分钟的 upstream 更新为 origin/dev
### 取消关联
- unset
    - git branch --unset-upstream
- unset other
    - git branch --unset-upstream dev

## 更新
### 仓库
- git clone ssh-url
### 所有
- git pull
### 指定分支
- git pull origin branch

## 合并
### 分支
- git merge
    - git merge `<dev>`： 将 dev 分支快速合并到当前分支
    - git merge [ ff | no-ff ] dev：默认使用 ff ，即 fast-forward ， 当删除分支 dev 后，则无法再次查看 dev 分支的日志等
- [git rebase](file:///D:/Program%20Files/Git/mingw64/share/doc/git-doc/git-rebase.html)
    - 当前分支 dev ： 将 dev 分支历次提交转成补丁，并以 master 分支最后一个提交为出发点逐个应用补丁。即使 dev 提交历史成为 master 分支的直接下流。
    - flow
        - git rebase master client
            - 将 client 补丁至 master
            - git checkout master
            - git merge client
    - args
        - git rebase --onto master server client
            - master->` server->` client
            - 跳过 server 直接将 client 补丁至 master
        - git rebase --continue：冲突处理后调用，继续完成
        - git rebase --abort：放弃
        - git rebase -i HEAD~2：合并最后两次提交
        - git rebase --skip：直接使用分支取代当前分支


### 提交
- git cherry-pick
    - `<commit id>`：将其它分支指定提交合并至当前分支，适用于通用分支 bug 的修复
    - 注：会引起冲突！
- git merge `<commit id>`

## 回滚
### 按版本
- git reset --hard
    - commitId
    - HEAD：当前分支最新版本
    - HEAD^：上一版本
    - HEAD^^：上上版本
    - HEAD~10：上第十个版本
### 按文件
- git checkout
    - -- files： 如 git checkout -- /src/*
### 按提交
- git revert `<commit id>`：撤消指定提交
    + -m parent-number: 指定回滚时的指定主分支
        * -m 1：默认当前分支
        * -m 2: 清除但保留主分支内容


## 删除
### 文件
- git rm files
- git commit -m "commit content"
### 分支
- git branch
    - -d branch： 删除分支
    - -D branch：强制删除分支，放弃修改

## 暂存
- git stash
    - 将当前代码暂存起来，便于切换到其它分支
    - list：显示当前分支下所有列表
    - drop：删除暂存内容
    - pop：恢复，并删除暂存内容

## 维护
### 性能
- git gc： 压缩历史命令
### 可靠性
- git fsck：系统文件检查
    - --lost-found：显示丢弃提交
- git show `<commit id>`：显示该提交的改变内容
- git merge`<commit id>`：恢复到之前提交
    - 注：有可能会有冲突

## 报错
### push时遇到unpack failed: error Missing tree错误
- 本地索引损坏，需重新构建
    - git gc
    - git pull --rebase
