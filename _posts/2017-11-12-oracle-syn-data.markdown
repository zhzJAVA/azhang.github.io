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

###### 用户授权
当用户要访问其它服务器数据库表中的数据时，本地数据库中必须创建了远程数据库的DB link，通过DB link本地数据库可以像访问本地数据库一样访问远程数据库表中的数据。
<br>
在创建DB link之前用户必须有创建DB link的权限。可先直接通过系统用户登录，然后执行以下语句：
<strong>
<br>
grant create  database link to {user} ;
<br>
grant create public  database link to {user} ;
<br>
</strong>
###### 创建DB link
登录<strong>{user}</strong>，使用以下语句建立DB link：
<br>
<strong>
create public database link {linkName}
<br>
connect to {user} identified by {userPwd} using
<br>
'(DESCRIPTION =
<br>
　　(ADDRESS_LIST =
<br>
　　　(ADDRESS = (PROTOCOL = TCP)(HOST = {targetDBIp})(PORT = 1521))
<br>
　　)
<br>
　　(CONNECT_DATA = (SERVICE_NAME = orcl))
<br>
)';
<br>
PS：可复制在本地数据库tnsnames.ora文件中配置了要远程访问的数据库的相关信息。
<br>
</strong>
<br>
例子：
<br>
<strong>
create public database link link71to65
<br>
connect to zhz identified by zhz using
<br>
'(DESCRIPTION =
<br>
　　(ADDRESS_LIST =
<br>
　　　(ADDRESS = (PROTOCOL = TCP)(HOST = {192.168.0.71})(PORT = 1521))
<br>
　　)
<br>
　　(CONNECT_DATA = (SERVICE_NAME = orcl))
<br>
)';
<br>
--link71to65是你创建的DBlink名字
<br>
--zhz/zhz是登录到远程数据库的用户/密码。
<br>
</strong>
<br>
然后在本地数据库中通过DBlink访问远程数据库'71'中的表，sql语句如下所示：
<br>
<strong>
select * from zhz.tableName@link71to65;
<br>
</strong>
### 创建存储过程

可以参考以下的例子：
<br>
<strong>
create or replace procedure pro_synData_71to65
<br>
as
<br>
begin
<br>
insert into local_table (local_column1,local_column2,local_column3,local_column4)select
<br>
target.column1,target.column2,target.column3,target.column4,target.column5 from
<br>
target_table@syn71to65 target
<br>
[where target.time >sysdate-30/(24*60) and (SELECT COUNT(1) FROM local_table WHERE local_column1 = target.column1)<1];
<br>
COMMIT;
<br>
END;
</strong>
<br>
在上面的例子中，<strong>target.time >sysdate-30/(24*60)</strong>　表示获取目标数据表半小时内的数据，
<strong>(SELECT COUNT(1) FROM local_table WHERE local_column1 = target.column1)<1</strong>　是为了防止数据重复，
其中target.time可以是目标数据表的某个时间字段，防止数据重复可以通过主键去判断。
当然，还是要根据具体的业务作处理。

### 创建定时任务
oracle创建job语句如下：
<br>
<strong>
DECLARE job_id pls_integer;
<br>
BEGIN
<br>
sys.dbms_job.submit
<br>
(job => job_id, what => 'pro_synData_71to65',next_data => SYSDATE,interval => 'SYSDATE+30/(60*24)',TRUE);
<br>
end;
<br>
</strong>
最后，启动job
<br>
<strong>
begin
<br>
SYS.dbms_job.run(26); --启动JOB
<br>
end;
<br>
关闭job可以通过
<br>
dbms_job.broken(26,false,sysdate); --停止JOB
<br>
RUN(参数)是我们建立任务的时候自动生成的任务号，指定任务号方可启动任务和删除任务
</strong>
<br>
如果对数据同步的实时性要求比较高的话，可以考虑采用触发器的方式去处理。此处不作探讨，有兴趣可以查找相关资料！

---