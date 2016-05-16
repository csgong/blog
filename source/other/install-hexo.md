---
title: Windows下 hexo+github pages 博客搭建
date: 2014-05-07 18:44:12
tags: 
 - github
 - hexo
 - 博客
categories: other
---
## 1. 前言

一直想搞个自己的博客,但是不知道哪个好，于是google之,看见很多人说[Github Pages](https://pages.github.com/)这个东西，于是顺藤摸瓜，查看到了很多这方便的东西.
我首先折腾的是[Jetyll](http://jekyll.bootcss.com/)，折腾中发现了[Octopress](http://octopress.org/)，最后是[Hexo](https://hexo.io/),Hexo 相对于前两个来说简单很多，对于我这个嫌麻烦的人来说，东西越简单越好.
<!-- more --> 
## 2. 关于Hexo，Github Pages
Jetyll、Octopress、Hexo是静态页面生成工具,通过简单的几行命令，就能将我们用markdown书写的文件快速的生成静态页面，而且他们支持Github Pages,Github Pages是Github 上每个projects的站点，官方的说法就是 
> Websites for you and your projects.

## 3. 环境准备
* 安装nodejs
我安装的是4.4.0，查看本本可以通过如下命令：
``` bash
$ >node --version
v4.4.0
```
* 安装hexo
``` bash
$ > npm install hexo-cli -g
```
* 创建项目结构
``` bash
$ > hexo init blog
$ > cd blog
$ > npm install
```
* 生成静态页面
``` bash
$ > hexo generate
```
* 查看生成的页面
``` bash
$ > hexo server(或者s)
INFO  Start processing
INFO  Hexo is running at http://localhost:4000/. Press Ctrl+C to stop.
```
打开浏览器输入`http://localhost:4000/`就可以看到默认的页面了
## 4. 部署到Github Pages
* 注册Github
* 创建Repository
新建Repository要注意，Repository name必须是`username.github.io`这种格式，例如，你的用户名是fandi,则你的Repository name是`"fandi.github.io"`.
* 配置hexo
想要将hexo生成的页面部署到Github Pages，如需要在配置文件中配置你的github信息。
打开`_config.yml`文件，添加如下配置：
```json
deploy:
  type: git
  repo: https://github.com/fandi/fandi.github.io.git
  branch: master
  message: "fandi commit"
```
* 安装部署插件
``` bash
$ >npm install hexo-deployer-git --save
```
* 部署
``` bash
$ > hexo deploy
```
发生错误，类似`FATAL bad indentation of a mapping entry at line 82, column 7`这种，则是你的配置文件格式错误，**每个属性后面都要有一个空格**
部署好后就可以访问了，在浏览器中输入`http://fandi.github.io/`就可以看见自己的博客了哦！
## 5. 发布新文章
``` bash
$ > hexo new "title"
```
通过这样一行命令，就可以在`source`目录下创建一个title.md文件，
编辑好后，运行`hexo generate`和`hexo deploy`就可以将文章部署到Github Pages上
More info: [Hexo doc](https://hexo.io/docs/)
