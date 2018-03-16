title: 搭建hexo博客
tags:
  - java
  - python
  - tools
categories: []
author: jackcooper
date: 2018-03-15 21:08:00
---

* * *

[hexo](https://github.com/hexojs/hexo)是一款基于Node.js的静态博客框架。hexo在npm官网上是这样介绍自己的`A fast, simple & powerful blog framework, powered by Node.js`。
在尝试使用hexo之前，请各位务必完成以下几点：

*   [安装node.js](http://nodejs.cn/download/)

*   安装[Git](https://git-scm.com/)

*   会使用Git

*   拥有GitHub账号

## 下载和安装hexo

1.  用管理员权限打开Git bash并输入`npm install  hexo-cli  -g`

2.  在D盘新建一个空文件夹名为hexo

3.  在Git Bash中进入hexo文件夹后输入`$ hexo init`
    * 在win10中如意出现找不到hexo命令，此时`需要找到hexo.cmd文件夹路径，添加到系统变量Path最后`，然后再执行该命令即可成功。

## 部署至github

在部署至github前先新建一个仓库，如`blog`。
安装`hexo-deployer-git`：

```
$ npm install hexo-deployer-git --save
```

修改`_config.yml`文件

```
deploy:
  type: git
  repository: <repository url>
  branch: master
```

生成静态文件并发布

```
$ hexo g -d
```

注：如果在之前你有生成ssh公钥，那么就可以直接提交上去，如果没有，则需要填写邮箱以及密码

## 更改主题

安装主题
进入hexo文件夹下输入命令

```
$ git clone https://github.com/A-limon/pacman.git themes/pacman

```

修改`_config.yml`文件

```
theme: pacman

```

现在可以运行`$ hexo s`命令启动服务器，在浏览器中访问地址`http://localhost:4000`就可以查看博客了。

---
---
hexo中文官网：https://hexo.io/zh-cn/docs/
hexo配置：http://blog.csdn.net/tonydandelion2014/article/details/61615898