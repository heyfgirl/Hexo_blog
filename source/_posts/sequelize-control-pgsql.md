---
title: sequelize操纵Pgsql
date: 2019-01-25 14:17:51
tags: 
- sequelize
- pgsql
categories:
- [ORM]
---
# Sequelize介绍
---
[Sequelize](https://itbilu.com/nodejs/npm/V1PExztfb.html)  是一个基于 promise 的 Node.js ORM, 目前支持 Postgres, MySQL, SQLite 和 Microsoft SQL Server. 它具有强大的事务支持, 关联关系, 读取和复制等功能.

---
[PostgreSQL](https://www.postgresql.org/docs/) 是一种非常先进的对象-关系型数据库管理系统（ORDBMS），目前功能最强大，特性最丰富和最先进的自由软件数据库系统。有些特性甚至连商业数据库都不具备。这个起源于伯克利（BSD）的数据库研究计划目前已经衍生成一项国际开发项目，并且有非常广泛的用户。


## 建立链接
---
  Sequelize会在初始化时设置一个连接池，这样你应该为每个数据库创建一个实例：

```javascript
var sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql'|'mariadb'|'sqlite'|'postgres'|'mssql',

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },
});
```
// 或者可以简单的使用一个连接 uri
```javascript
var sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname');
```

```javascript
  var User = sequelize.define('user', {
    firstName: {
      type: Sequelize.STRING,
      field: 'first_name' // Will result in an attribute that is firstName when user facing but first_name in the database
    },
    lastName: {
      type: Sequelize.STRING
    }
  }, {
    freezeTableName: true // Model 对应的表名将与model名相同
  });

  //将表同步到数据库
  User.sync({force: true}).then(function () {
    // 已创建数据表
    return User.create({
      firstName: 'John',
      lastName: 'Hancock'
    });
  });
```
---
  Sequelize是基于Promise实现的流程控制

```javascript  
  let user = await User.findOne();
  User.findOne().then(user =>{
    console.log(user);
  });
```

##  数组数据的检索


```javascript
  //检索语句是被检索数据的子集
  $contains: [1, 2]
  //被检索数据是检索语句的子集
  $contained: [1, 2]
  //被检索数据与检索语句有交集
  $overlap: [1, 2]
```
---
