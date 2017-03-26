---
title: '博客搭建 : hexo + GitHub + 多机同步'
date: 2017-03-26 12:50:02
tags: hexo
categories: 博客

---
# 博客搭建 : hexo + GitHub + 多机同步  

记录 [hexo](hexo_url) 博客搭建及如何实现多机同步；

 ## [安装 Git](git_url)   

安装完后，打开 Git bash , 配置用户名和邮箱

 ```   
  $ git config --global user.name "William"
  $ git config --global user.email William@example.com
  输入 git --version 显示版本号则安装成功
 ```
 ## [安装 Node.js](node_url)  

  输入 node -v 和 npm -v，如果显示版本号则安装成功；

 ## [GitHub 仓库创建](github_url)

   1. 创建仓库，命名为 "GitHub Username".github.io; 注意 GitHub Username 必须为 GitHub 的用户名；

   2. 建好之后，该仓库默认有个分支 master (用来存放生成的静态网页), 再创建一个分支命名为 hexo (用来存放生成网站的原始文件)，并将 hexo 分支设置为 默认分支  

   3. 使用 `git clone git@github.com:username/username.github.io.git` 拷贝仓库  

   4. 使用 Git bash 进入本地仓库 username.github.io.git(此时分支应为 hexo )， 依次执行 `npm install hexo`,`hexo init`,`npm install`,`npm install hexo-deployer-git`  

   5. 修改 `_congig.yml` 的 deploy 参数，分支应为 master ,表示生成的静态网页文件将被推送到 master 分支上；

   ```
  deploy:
   type: git
   repo: https://github.com/username/username.github.io.git
   branch: master

   ```  

   6. 执行 `git add .`,`git commit -m "提交信息"`,`git push origin hexo`

   7. 执行 `hexo g -d` 将生成的网站部署到 GitHub 上；

## 平时维护
  1. 本地添加博客或修改主题后，执行 `git add .`,`git commit -m "提交信息"`,`git push origin hexo`将修改推送到远程分支 hexo 上；
  2. 使用 hexo g 生成静态文件， hexo s 开启服务器预览 ，浏览器输入 http://localhost:4000 即可预览；使用 hexo d 将生成的静态文件部署到 master 分支上；

## 多机维护
  1. 在另一台电脑上，将未安装的软件先安装，之后执行 `GitHub 仓库创建` 的  2，3步（第三步不需要执行 hexo init 命令）

## 遇到的问题
  1. `Error: bash: /dev/tty: No such device or address`， 因为我在 cmd 去进行上述操作，该成 Git bash 之后就没有该问题了；

  2. `hexo command not found`, 使用 `npm install -g hexo` 安装 hexo

  3. 第二台电脑同步部署完成之后，打开网站空白，原因是主题文件家为空，在 `_congif.yml` 中指定的主题不存在. 解决方案,重新 clone 一份


## 最后  

 1. 推荐使用 [atom](atom_url) 编辑 md 文件；


  [git_url] : (https://www.git-scm.com/download)  

  [hexo_url] : (https://hexo.io/zh-cn/docs/index.html)

  [github_url] : (https://github.com)  

  [node_url] : (https://nodejs.org/en)

  [atom_url] : (https://atom.io)  
