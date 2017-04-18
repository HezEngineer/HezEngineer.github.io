---
title: git
date: 2017-04-06 08:26:08
tags: 版本控制
---

# 基本命令行

 1. 打开GitBash;配置在服务端(GitLab/GitHub)注册用户名和邮箱  

 ```
 git config global user.name "注册的用户名"z
 git config global user.email "注册的邮箱"

 ```

 2. 如果本地还没有仓库，使用GitBash ，进入想要存放项目的文件夹；  

 ```
 git clone "远程仓库地址.git"
 cd "项目根文件夹名称"
 git add .
 git checkout --file 取消暂存区域的文件；
 git commit -m "提交信息"
 git push -u origin master

 ```
 3. 如果本地新建仓库

 ```
  cd "想要存放项目的文件夹"
  仓库初始化--- git init
  关联远程仓库--- git remote add origin "远程仓库地址.git"
  将所有文件添加到暂存区中--- git add .
  提交到本地版本库中---git commit
  将提交推送到远程分支--- git push -u origin master

 ```

 4. 分支

 ```
    * 创建--- git branch "分支名字
    * 切换--- git checkout "想要切换到的分支"
    * 删除--- git branch -d "想要删除的分支"
    * 创建并切换--- git checkout -b "想要创建的分支的名字"
    * 查看提交日志--- git log "--graph 或 --oneline"
    * git log "分支1".."分支2" 表示分支2有那些提交没有合并到分支1

 ```

 5. 合并

 ```
  *  在这个分支下，合并其他分支的修改到本分支；git merge 参数 "想要合并的分支" ；
      1. --fastforward(默认的合并方式; 简写为: -ff)
      2. --no--ff
      3. --squash 与前两者区别，不会看出来从其他分支的合并过程；

  *  在这个分支下，将自己的修改合并到其他分支；git rebase "想要变基的分支名称"

  * cherry-pick commitId 将某个分支的某次提交合并到所在的分支；

  * git reset --hard（soft,mixed） commitID；回退到某个版本，包括index,工作区域，HEAD指针；

 ```

 6. 关联远程分支

 ```
  1. git branch --set-upstream-to=origin/关联的远程分支的名字
  2. 更新 git pull origin 关联的远程分支的名字
  3. 推送 git push origin 关联的远程分支的名字

 ```
