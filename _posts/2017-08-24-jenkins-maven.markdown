---
layout:     post
title:      "Jenkins+maven的使用"
subtitle:   "jenkins+maven自动构建"
date:       2017-08-24
author:     "Zhz"
header-img: ""
catalog:    IDE
tags:
    - jenkins
    - maven
---

> “ 14级大风... 愿生活善待每个还在努力的人！ ”


### windows下jenkins+maven的自动构建

###### 安装jenkins并安装maven插件

这里不做赘述，可以参考我的<a href="/2017/08/23/jenkins-ant/">另一篇文章</a>。

浏览器打开jenkins，系统管理>>管理插件>>安装Maven Integration plugin插件，安装完成后在新建job时就会多出一个maven的相关选项。
<img src="/img/in-post/20170824_jenkins/1-1-1.png"/>
###### 安装maven

maven官网<a href="http://maven.apache.org/download.cgi">http://maven.apache.org/download.cgi</a>下载maven压缩包
<br>
可以看到3.3及以上版本需要1.7及以上的jdk版本
<br>
下载完成之后解压，在系统环境变量下添加MAVEN_HOME：D:/apache-maven-3.3
<br>
cmd下运行mvn -v，显示版本号即表示安装成功
<img src="/img/in-post/20170824_jenkins/1-2-1.png"/>
打开jenkins，系统管理>>>Global Tool Configuration>>>
<br>
添加jdk，这里可以添加多个不同版本的jdk，最好先在本地下载好jdk，不要选择自动安装，以防网络问题影响安装效率。
<img src="/img/in-post/20170824_jenkins/1-2-2.png"/>
添加maven：
<img src="/img/in-post/20170824_jenkins/1-2-3.png"/>

###### 使用Nexus搭建maven私服
搭建Maven私有仓库的主要目的：
1、有的公司不能访问外网，只能通过私服来管理jar包和插件；
<br>
2、公司网速比较慢，通过公司的私服来获取jar包比较快；
<br>
3、内部的的一些jar包放在私服上，比较方便的大家使用。
<br>

###### pom.xml

pom作为项目对象模型，通过xml表示maven项目，使用<a href="">pom.xml</a>来实现。
<br>
主要描述了项目：包括配置文件，开发者需要遵循的规则，缺陷管理系统，组织和licenses，项目的url，项目的依赖性，以及其他所有的项目相关因素

这里要提到的是关于使用jenkins构建maven项目时遇到的关于pom.xml的问题：
<br>
<b>问题：</b>构建的时候报错：“警告：File encoding has not been set, using platform encoding GBK, i.e. build is platform dependent!”
<br>
<b>解决方案：</b>这个是编码不一致的问题，需要在pom.xml添加配置项
<img src="/img/in-post/20170824_jenkins/1-4-1.png"/>
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>
<b>问题：</b>构建的时候出现“警告：xxx 是 Sun 的专用 API，可能会在未来版本中删除”
cmd下运行mvn clean install -X，还会出现以下错误
<img src="/img/in-post/20170824_jenkins/1-4-2.png"/>
错误在于plexus-compiler-javac这个模块中，当遇到中文警告信息时均判断成CompileError，直接导致maven-compile-plugin抛出编译错误。
<br>
<b>解决方案：</b>
网上找到的答案大多数maven打包时出现此问题的解决方式，pom.xml配置maven release的maven-compiler-plugin  2.3.2或者以上。
<img src="/img/in-post/20170824_jenkins/1-4-3.png"/>
但是在jenkins下并不能解决此问题，此问题的关键在于项目中使用了jdk1.6的sun专用jar包，如果想要项目构建时可以使用1.6以上的jdk，并且可以正常构建包含sun专用jar包的maven项目
则需要在pom.xml将以上的配置改为，
<br>
<img src="/img/in-post/20170824_jenkins/1-4-4.png"/>
强制引用rt.jar即可

###### jenkins中新建maven任务

jenkins中新建maven任务
<img src="/img/in-post/20170824_jenkins/1-5-1.png"/>
<img src="/img/in-post/20170824_jenkins/1-5-2.png"/>
<img src="/img/in-post/20170824_jenkins/1-5-3.png"/>
<img src="/img/in-post/20170824_jenkins/1-5-4.png"/>
<img src="/img/in-post/20170824_jenkins/1-5-5.png"/>
至此，新建maven任务成功

---