---
layout:     post
title:      "关于Oracle表空间，用户，权限... ..."
subtitle:   "表空间｜用户｜权限｜表格 一二点"
date:       2017-11-11
author:     "Zhz"
header-img: "img/in-post/post_head/20171111_oracle.gif"
catalog:    DATABASE
tags:
    - oracle
---

> “ 积极学习，拥抱变化！”

### 常用的查看Oracle数据库状态的语句

###### 查看当前用户的权限
select * from session_privs;
###### 查看当前用户的表
select table_name from user_tables;
###### 查看所有用户的表
select table_name from all_tables;
###### 查看系统表
select table_name from dba_tables;
###### 查看表字段
select * from user_tab_columns where Table_Name='表名称';
###### 查看表注释
select * from user_tab_comments;
###### 查看字段注释
select * from user_col_comments where Table_Name='表名称';
###### 查看所有用户
select * from all_users;
###### 查看当前用户
select * from user_users;
###### 查看用户系统权限
select * from user_sys_privs;
###### 查看当前用户定时任务
select * from user_jobs;


### Oracle 表空间、用户、对象权限、系统权限

###### 表空间
语法：
<strong>
<br>
&nbsp;&nbsp;CREATE TABLESPACE tablespacename
<br>
&nbsp;&nbsp;DATAFILE 'filename'[size integer]
<br>
&nbsp;&nbsp;[AUTOEXTEND[OFF|ON]]
<br>
&nbsp;&nbsp;tablespancename：表示创建表空间的名称。
<br>
&nbsp;&nbsp;filename：表示组成表空间的一个活多个数据文件，当有多个数据文件时，使用，分隔。
<br>
&nbsp;&nbsp;size：指定文件的大小，单位是K（字节）或者M（兆）。
<br>
&nbsp;&nbsp;AUTOEXTEND：用来启用或警用数据文件的自动扩展，只有设置了AUTOEXTEND后，在存储空间使用完毕后才会自动扩展，设置为ON即可，如果设置为OFF，则不能自动扩展。会导致存储空间不足的情况。
<br>
</strong>
例子：
<strong>
<br>
&nbsp;&nbsp;CREATE TABLESPACE ODIN_ITMS_DATA DATAFILE
<br>
&nbsp;&nbsp;&nbsp;&nbsp;'E:/app/Administrator/oradata/ITMS_DATA.DBF' SIZE 1G AUTOEXTEND ON NEXT 100M MAXSIZE 10G,
<br>
&nbsp;&nbsp;&nbsp;&nbsp;'E:/app/Administrator/oradata/ITMS_DATA_01.DBF' SIZE 1G AUTOEXTEND ON NEXT 100M MAXSIZE 5G
<br>
&nbsp;&nbsp;LOGGING
<br>
&nbsp;&nbsp;ONLINE
<br>
&nbsp;&nbsp;EXTENT MANAGEMENT LOCAL AUTOALLOCATE
<br>
&nbsp;&nbsp;BLOCKSIZE 8K
<br>
&nbsp;&nbsp;SEGMENT SPACE MANAGEMENT AUTO
<br>
&nbsp;&nbsp;FLASHBACK ON;
<br>
</strong>
###### 用户与模式

用户：对数据库的访问，需要以适当用户身份通过验证，并具有相关权限来完成一系列动作
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SYS用户，缺省始终创建，且未被锁定，拥有数据字典及其关联的所有对象
<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;SYSTEM用户，缺省始终创建，且未被锁定，可以访问数据库内的所有对象
<br>
    模式(schema)：是某个用户拥有所有对象的集合。具有创建对象权限并创建了对象的用户称为拥有某个模式
<br>
    <span><strong>注意：创建数据库对象(视图,表等)的任一用户都拥有一个以该用户名称开头的模式，且被视为模式用户</strong></span>

###### 创建及修改用户
条件：<strong>需要具有创建用户的权限，如sys,system,sysdba,dba role等</strong>
<br>
语法：
<strong>
<br>
&nbsp;&nbsp;CREATE USER user
<br>
&nbsp;&nbsp;IDENTIFIED {BY password | EXTERNALLY | GLOBALLY AS external name }
<br>
&nbsp;&nbsp;[DEFAULT TABLESPACE tablespace_name]
<br>
&nbsp;&nbsp;[TEMPORARY TABLESPACE tablespace_name]
<br>
&nbsp;&nbsp;[QUOTA {n {[K|M] | UNLIMITED } ON tablespace_name
<br>
&nbsp;&nbsp;QUOTA {n {[k|M] | UNLIMITED } ON tablespace_name ... ]
<br>
&nbsp;&nbsp;[PASSWORD EXPIRE]
<br>
&nbsp;&nbsp;[ACCOUNT { LOCK | UNLOCK }]
<br>
&nbsp;&nbsp;[PROFILE { profile_name | DEFAULT }]
<br>
</strong>
例子:
<br>
<strong>
&nbsp;&nbsp;CREATE USER ZHZ
<br>
&nbsp;&nbsp;IDENTIFIED BY zhz
<br>
&nbsp;&nbsp;DEFAULT TABLESPACE ITMS_DATA
<br>
&nbsp;&nbsp;TEMPORARY TABLESPACE ITMS_TEMP
<br>
&nbsp;&nbsp;PROFILE DEFAULT
<br>
&nbsp;&nbsp;ACCOUNT UNLOCK;
</strong>

###### Oracle权限

系统权限: 允许用户执行特定的数据库动作，如创建表、创建索引、连接实例等
<br>
对象权限: 允许用户操纵一些特定的对象，如读取视图，可更新某些列、执行存储过程等
<br><br>
常用的系统权限：
<strong>
<br>
       CREATE SESSION                     创建会话
<br>
       CREATE SEQUENCE                    创建序列
<br>
       CREATE SYNONYM                     创建同名对象
<br>
       CREATE TABLE                       在用户模式中创建表
<br>
       CREATE ANY TABLE                   在任何模式中创建表
<br>
       DROP TABLE                         在用户模式中删除表
<br>
       DROP ANY TABLE                     在任何模式中删除表
<br>
       CREATE PROCEDURE                   创建存储过程
<br>
       EXECUTE ANY PROCEDURE              执行任何模式的存储过程
<br>
       CREATE USER                        创建用户
<br>
       DROP USER                          删除用户
<br>
       CREATE VIEW                        创建视图
<br><br>
</strong>
授予用户系统权限
<strong>
<br>
       GRANT privilege [, privilege...] TO user [, user| role, PUBLIC...]
<br>
       [WITH ADMIN OPTION];
<br>
       PUBLIC     所有用户
<br>
       WITH ADMIN OPTION 使用户同样具有分配权限的权利，可将此权限授予别人
</strong>
<br><br>
不同的对象具有不同的对象权限，
       对象的拥有者拥有所有权限，
       对象的拥有者可以向外分配权限。
<br>
常用的对象权限：
<strong>
<br>
　alter(修改)  delete (删除)    select(查询)     insert(添加)
<br>
　update   (修改)  index  (索引)    references  (引用)     execute  (执行)
<br>
</strong>
<br>
对象授权
<strong>
<br>
       GRANT object_priv｜ALL [(columns)]
<br>
       ON object
<br>
       TO {user|role|PUBLIC}
<br>
       [WITH GRANT OPTION];
<br>
       ALL：所有对象权限
<br>
       PUBLIC：授给所有的用户
<br>
       WITH GRANT OPTION：允许用户再次给其它用户授权
---