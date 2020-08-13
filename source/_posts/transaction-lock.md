---
title: MySql和postgres的事务以及锁
date: 2019-08-13 16:02:28
tags:
- sql
- postgres
- mysql
- 事务
- 锁
---

显示事务级别
show transaction_isolation;


更改数据库事无级别
set transaction ISOLATION LEVEL   【read committed】【repeatable READ】【serializable】；
一、隔离问题及隔离级别

1、隔离问题：
a、脏读：一个事务读到另一个事务没有提交的数据

b、不可重复读：一个事务读到另一个事务已提交的数据（update、delete：一个事务重新执行查询，发现数据因被另一个已经提交的事务 update 操作而改变）

c、虚读(幻读)：一个事务读到另一个事务已提交的数据（insert：一个事务重新执行查询，发现查询结果的集合因另一个已提交事务的 insert 操作而改变）

2、隔离级别

a、read uncommitted：读未提交。存在3个问题

b、read committed：读已提交。解决脏读，存在2个问题可以

      postgreSQL、oracle默认：可以看到已提交的；第二个事务等第一事务提交后提交

      (注：select看到的是select开始之前数据库的快照)

c、repeatable read：可重复读。解决：脏读、不可重复读，存在1个问题。

     mysql：show global variables like 'tx_isolation';

     mysql默认：看不到已提交的；第二个事务等第一个事务提交后回滚（exception：concurrent update）

d、serializable ：串行化。都解决，单事务（如redis的单线程）。

        (注：select看到的是事务开始之前数据库的快照)

3、SQL标准允许的：4种隔离级别只定义了哪种现象不能发生，但是没有定义哪种现象一定发生


隔离级别	脏读	幻读	不可重复性读取
读未提交	可能	可能	可能
读已提交	不可能	可能	可能
可重复读	不可能	可能	不可能
可串行读	不可能	不可能	不可能
二、操作

1、linux客户端进入：./psql -h localhost -p 5444 -U rimu rimu  进去后输入密码（./psql -h ip- p port -U 用户名 数据库名）

      linux客户端退出：quit

2、开启事务：begin;

3、提交事务：commit;

4、查看postgreSQL默认的隔离级别：show default_transaction_isolation;

5、查看postgreSQL当前的隔离级别：show transaction_isolation; 

6、修改当前事务的隔离级别：set transaction isolation level 四种级别;

7、修改当前回话的隔离级别：set default_transaction_isolation = '四种级别';

 

三、测试

1、参考 postgresql事务处理与并发控制 ：https://blog.51cto.com/fengfeng688/2153042

2、官方文旦：https://www.postgresql.org/docs/9.4/static/transaction-iso.html

3、测试结果：

repeatable read

serializable

read committed

更新同一表中的同一数据	先后开启事务A、B。A更新a，B更新a。后提交的会回滚：could not serialize access due to concurrent update	先后开启事务A、B。A更新a，B更新a。后提交的会回滚：could not serialize access due to concurrent update	先后开启事务A、B。A更新a（0.109 ms），B更新b（0.113 ms）。A和B都会commit成功（但B会等A执行成功且commit之后，B才会执行成功，最后B也提交）
更新同一表中的不同数据	先后开启事务A、B。A更新a（0.197 ms），B更新b（0.202 ms）。A和B都会commit成功	
先后开启事务A、B。A更新a（0.233 ms），B更新b（0.274 ms）。后提交的会回滚：

ERROR: could not serialize access due to read/write dependencies among transactions
DETAIL: Reason code: Canceled on identification as a pivot, during commit attempt.
HINT: The transaction might succeed if retried.

 

先后开启事务A、B。A更新a（0.109 ms），B更新b（0.113 ms）。A和B都会commit成功

四、MVCC

1、参考资料：https://blog.csdn.net/Habren/article/details/51592969

2、insert操作 : begin--->insert--->将数据放在share_cache中,并标记为dirty(脏读问题)--->commit--->将数据持久化到磁盘

3、SELECT xmin,xmax,cmin,cmax,age(xmin),txid_current from dual;

说明：txid_courrent= 2^32 + xmin + age(xmin);

            edb当前事务号= xmin+age(xmin)

            edb当前事务号到达2^32时，回归

            已提交的事务之间是否可见比较的是xmin的大小

