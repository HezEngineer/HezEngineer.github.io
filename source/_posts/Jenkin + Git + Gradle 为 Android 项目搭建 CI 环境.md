---
title: Jenkin + Git + Gradle 为 Android 项目搭建 CI 环境
date: 2017-04-06 08:26:08
tags: 项目管理 自动化构建
---

# [配置Tomcat][tomcat_url]
 * windows系统环境变量

  ![](../img/ci/tomcat.png)

  ![](../img/ci/tomcat1.png)

# [jenkins安装](jenkins_url)

  * jenkins 支持安装版和war包两种安装方式，这里介绍的是war包安装；

  * 下载.war文件，在tomcat安装目录下的webapps下新建jenkins文件夹，将war包放到该文件夹下；打开cmd,进入jenkins目录，执行`java -jar jenkins.war`，浏览器访问` localhost:8080 `,会在C盘对应用户目录下生成`.jenkins`目录；

# 基本配置

  * 遇到创建账户的问题，如果自己注册的账户失败可以使用默认账号`admin`和`C:\Users\登录用户\.jenkins\secrets\initialAdminPassword 中的账号登录`

  * 下载必要的插件后，进入系统管理->Global Tool Configuration，配置本地的jdk,Gradle,Android Git 路径;

  * 系统管理->系统设置->全局属性版块勾选上 `Environment variables` 选项，然后添加 `Android SDK` 的目录

# 触发构建

 *



 [tomcat_url]: http://tomcat.apache.org/
 [jenkins_url]:https://jenkins.io/
