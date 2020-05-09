---
layout:     post
title:      "Jenkins+ant的使用"
subtitle:   "jenkins+ant自动构建"
date:       2017-08-23
author:     "Zhz"
header-img: ""
catalog:    IDE
tags:
    - jenkins
    - ant
---

> “ 马男金句：值得期待的只有前方！ ”


### windows下jenkins+ant的自动构建

###### 下载安装jenkins

官网地址：<a href="http://Jenkins-ci.org/">http://Jenkins-ci.org/</a>
<br>
官网镜像地址：<a href="http://mirrors.jenkins-ci.org/war-stable/">http://mirrors.jenkins-ci.org/war-stable/</a> （推荐选择最新版下载）！

下载的是.exe跟着操作安装就行了。如果是war包可以新建jenkins目录，将war包复制进去，cmd终端下进入到该目录，运行命令：Java -jar jenkins.war（前提是系统要先配置了jdk）。

这样jenkins就安装成功了。然后我们打开一个浏览器，输入localhost:8080 就会弹出jenkins的web界面。作为开发人员，系统8080端口多会被用作其它用途，如果提示安装失败，就可以修改jenkins端口，
终端下运行命令：java -jar jenkins.war --ajp13Port=-1 --httpPort=8888(端口号)，确保端口号没有被占用就行了。

还可以将jenkins.war复制到Tomcat的webapps目录下
<br>
配置环境变量
<br>
新建JENKINS_HOME系统环境变量，变量值处输入JENKINS安装目录，如 "D:\jenkins"，
<br>
或者 编辑Tomcat的conf\context.xml，添加JENKINS_HOME环境变量。
<img src="/img/in-post/20170823_jenkins/1-1-1.png">

浏览器访问Jenkins，看看是否成功。

如果是通过.exe文件安装的，则可以在Jenkins安装路径下的Jenkins.xml文件中修改端口，重启jenkins服务即可。

###### jenkins安装插件

可以通过在浏览器中打开jenkins在线安装插件，系统管理>>>管理插件>>>
<img src="/img/in-post/20170823_jenkins/1-2-1.png">
根据需要选择插件安装，以下是几个插件的简单介绍：
<br>
1、ant插件
<br>
2、Maven Integration plugin 整合maven项目插件（安装之后在新建job时就会多出一个maven的相关选项）
<br>
3、Deploy to Container Plugin 发布war包到远程tomcat下
<br>
4、SSH plugin 远程Linux等系统执行shell命令
<br>
5、disk usage plugin 这个插件可以监控每次build项目所花费的物理空间 
<br>
6、thinbackup 这个插件可以备份你job的配置 
<br>
7、jenkins workspace cleanup plugin 这个插件可以再每次build之前清空workspace 
<br>
8、build-name-setter 这个插件可以设置每次build显示的名字 
<br>
9、git插件 
<br>
10、PMD插件 这个插件 
<br>
11、python plugin 这个插件支持你像写shell一样写python代码 
<br>
12、email-ext plugin 这个插件提供你发送HTML格式的邮件 
<br>
13、hudson next build number plugin 如果你想把hudson每次build的版本号放到你的产品版本号中你可以使用这个插件 
<br>
14、HTML publisher plugin 支持HTML格式的report 
<br>
15、android lint plugin  支持android项目检查 
<br>
16、Copy Artifact Plugin  支持把build后产生的结果copy到其他项目去 
<br>
17、promoted 支持手工测试验证，或者发布产品，在build结束后手动执行 
<br>
18、Jenkins description setter plugin  这个插件可以支持在Job的描述修改，例如二维码 
<br>
19、Hudson Port Allocator Plug-in  开启关闭端口，或者选择一个自由的端口来进行build 
<br>
20、Android Emulator Plugin 
<br>
21、JobConfigHistory Plugin  这个插件用于查看jobconfig历史记录 
<br>
22、Jenkins Sounds plugin  这个插件用于结束build后会给出声音提示 
<br>
23、JIRA Plugin  这个插件用来和Jira配合使用 

如果在线安装失败，可以选择手动安装，打开插件网址，搜索下载插件
<a href="https://wiki.jenkins.io/display/JENKINS/Plugins">https://wiki.jenkins.io/display/JENKINS/Plugins</a>
<img src="/img/in-post/20170823_jenkins/1-2-2.png">
<img src="/img/in-post/20170823_jenkins/1-2-3.png">
下载完后在系统管理>>>管理插件>>>高级>>>上传插件即可，如下图
<img src="/img/in-post/20170823_jenkins/1-2-4.png">

###### ant安装

官网下载ant：<a href="http://ant.apache.org/bindownload.cgi">http://ant.apache.org/bindownload.cgi</a>

下载完压缩包后解压，然后在环境变量中新增ANT_HOME：E:\apache-ant-1.7.0(ant根目录)。

在cmd运行ant -v，正常的话会显示对应的ant版本号，如果出现以下错误，则说明是jdk版本不符合ant的要求
<img src="/img/in-post/20170823_jenkins/1-3-1.png">
ant版本对jdk版本是有要求的，目前最新的1.10版本对应的jdk是1.8，ant-1.7支持jdk1.7，如果有jdk版本的要求，请自行降低ant版本。

###### jenkins中新建ant任务

打开jenkins，新建job，填写项目名字，选择“构建一个自由风格的软件项目”
<img src="/img/in-post/20170823_jenkins/1-4-1.png">
源码管理，配置svn地址，	添加svn用户名和密码
<img src="/img/in-post/20170823_jenkins/1-4-2.png">
构建触发器，可根据自己需要自行配置
<img src="/img/in-post/20170823_jenkins/1-4-3.png">
构建，选择Invoke ant，默认会读环境变量中ANT_HOME，选择后可保持默认
<img src="/img/in-post/20170823_jenkins/1-4-4.png">

构建后操作，选择Archive the artifacts，添加此选项之后就可以在jenkins界面下载war包！需要说明的是，我这里不需要自动部署，所以只添加了这个操作
<img src="/img/in-post/20170823_jenkins/1-4-5.png">

这里完成了jenkins通过ant简单的自动构建

关于build.xml的内容可以参考我以前的项目的xml文件

---