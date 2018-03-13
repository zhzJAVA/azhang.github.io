---
layout:     post
title:      "Jenkins自动部署项目"
subtitle:   "jenkins+tomcat自动部署(windows)"
date:       2018-03-13
author:     "Zhz"
header-img: ""
catalog:    IDE
tags:
    - jenkins
    - tomcat
---

> “ 工作中要有规范，作息上要有规律，学习里有要规划！坚毅，坚强，坚持！！！ ”


### jenkins安装插件

###### 安装插件“Deploy to container Plugin”

浏览器打开jenkins，系统管理>>>管理插件>>>找到并安装Deploy to container Plugin插件，安装成功后在job里面选择“构建后的操作”时就会多出一个选项。

<img src="/img/in-post/20180313_jenkins/1-1-1.png"/>
### jenkins配置自动部署到tomcat

###### tomcat添加用户

此处，不得不佩服jenkins强大，扩展性做得特别好，有很大功能强大的插件。其实配置很简单，只是有几个关键步骤需要注意：
<br>
首先在tomcat>>>conf>>>tomcat-user.xml中的tomcat-users标签中添加用户：
<img src="/img/in-post/20180313_jenkins/2-1-1.png"/>
{% raw %}
    <role rolename="manager-script"/>
    <role rolename="manager-gui"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <user username="tomcat" password="tomcat" roles="manager-gui,manager-script,manager-jmx,manager-status"/>
    以下是Tomcat Manager 4种角色的大致介绍(下面URL中的*为通配符)：
    manager-gui
    允许访问html接口(即URL路径为/manager/html/*)
    manager-script
    允许访问纯文本接口(即URL路径为/manager/text/*)
    manager-jmx
    允许访问JMX代理接口(即URL路径为/manager/jmxproxy/*)
    manager-status
    允许访问Tomcat只读状态页面(即URL路径为/manager/status/*)
{% endraw %}
可能tomcat不同版本的配置略有不同，此处在7.0中是适用的。

###### tomcat设定允许redeploy

对tomcat中能进行redeploy进行配置，在tomcat>>>conf>>>context.xml中修改以下配置
<br>
<b>antiJARLocking="true" antiResourceLocking="true"</b>
<img src="/img/in-post/20180313_jenkins/2-2-1.png"/>
此步骤比较关键，网上很多地方都没有提到，如果缺少这个配置，jenkins在deploy时会出现以下错误：
<img src="/img/in-post/20180313_jenkins/2-2-2.png"/>

###### jenkins配置部署到tomcat

在jenkins创建job时选择“构建后的操作”>>>Deploy war/ear to a container，填写以下内容：
<img src="/img/in-post/20180313_jenkins/2-3-1.png"/>
做完以上配置后，构建项目的时候即会自动把最新war包部署到tomcat中。

---