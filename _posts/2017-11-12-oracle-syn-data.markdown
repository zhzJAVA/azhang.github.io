---
layout:     post
title:      "Oracle同步数据库表数据"
subtitle:   "通过存储过程同步表数据"
date:       2017-11-12
author:     "Zhz"
header-img: "img/in-post/post_head/20171112_oracle.jpg"
catalog:    DATABASE
tags:
    - oracle
---

> “ 双11阿里一天卖出1682亿！马云：阿里双11不赚钱，就想给你们带来快乐！<br>我们：ennnnnnnn！ ”


### 建立database link

当用户要访问其它服务器数据库表中的数据时，本地数据库中必须创建了远程数据库的dblink，通过dblink本地数据库可以像访问本地数据库一样访问远程数据库表中的数据。
<br>
创建dblink一般有两种方式，不过在创建dblink之前用户必须有创建dblink的权限。可先直接通过系统用户登录，然后执行以下语句：
<br>
grant create  database link to {user} ;
<br>
grant create public  database link to {user} ;

######

---