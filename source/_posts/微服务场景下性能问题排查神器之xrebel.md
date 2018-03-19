title: 微服务场景下性能问题排查神器之xrebel
author: jackcooper
date: 2018-03-19 17:13:26
tags:
---
对于java应用性能跟踪其实有很多种手段，本文只是针对`xrebel`的使用做一些简单讲解（`单体应用`和`微服务应用`）。分布式跟踪有很多，比如zipkin等。zipkin太重，不适合开发阶段使用。本文介绍的xrebel是一个适合小团队开发时期使用的idea(Eclipse)插件。如果对zipkin想了解，可以参考[dubbo分布式系统链路追踪_zipkin](http://www.jianshu.com/p/85f23233345e).

### xrebel下载
* **百度云（内含激活文件）**：链接: https://pan.baidu.com/s/1qYvBDMG   密码: fefb 
* 官方地址：https://zeroturnaround.com/software/xrebel/download/

### 快速安装
 1. 下载xrebel.zip, 并解压到本地，例如：`D:/xrebel`  。**注意路径中不要有中文不要有空格**
 1. 在tomcat也好，idea,eclipse也好，修改vm 参数，添加 `-javaagent:[path/to/xrebel]/xrebel.jar`

### 配置demo
* idea
![image.png](http://upload-images.jianshu.io/upload_images/3710706-69fe7557a03107e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* eclipse
![image.png](http://upload-images.jianshu.io/upload_images/3710706-944070de1e2684a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

默认是可以试用14天的，建议支持正版，毕竟大家都是吃这行饭的。上述下载方式中百度云里下载包含破解文件。具体破解方式参考下文。



### 启动项目
1、项目启动时控制台出现如下日志
```txt
Connected to the target VM, address: '127.0.0.1:56732', transport: 'socket'
XRebel: Starting logging to file: C:\Users\10400\.xrebel\xrebel.log
2017-12-21 15:40:16 XRebel: 
2017-12-21 15:40:16 XRebel: ################################################################
2017-12-21 15:40:16 XRebel: 
2017-12-21 15:40:16 XRebel:  XRebel 3.1.3 (201607291040)
2017-12-21 15:40:16 XRebel:  (c) Copyright ZeroTurnaround AS, Estonia, Tallinn.
2017-12-21 15:40:16 XRebel: 
2017-12-21 15:40:16 XRebel:  For questions and support, contact xrebel@zeroturnaround.com
2017-12-21 15:40:16 XRebel: 
2017-12-21 15:40:16 XRebel: ################################################################
2017-12-21 15:40:16 XRebel: 
2017-12-21 15:40:16.719 [auth-service] [main] WARN  [com.br.auth.AuthApplication] -启动demo服务
2017-12-21 15:40:17.027 [auth-service] [restartedMain] WARN  [com.br.auth.AuthApplication] -启动demo服务
...........
```

2、项目页面左下角有如下图标
![image.png](http://upload-images.jianshu.io/upload_images/3710706-a3a2fe7b874b445c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 激活xrebel
![demo.gif](http://upload-images.jianshu.io/upload_images/3710706-2da3b951f722c78b.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### xrebel 简单使用教程

打开 web 服务页面，xrebel会直接注入到你的页面中，左下角会出现`xrebel`的`toobar`，例如：http://localhost:18601。或者通过 访问`服务/xrebel` 打开单独页面，适用于webservice,restful 等无页面场景。

![image.png](http://upload-images.jianshu.io/upload_images/3710706-8cde618f757ff656.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/3710706-650abe4501368fdd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/3710706-06eb23efd6c8dc8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/3710706-5a74eaa7d1bada63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/3710706-8b1fb0c923faf2c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
