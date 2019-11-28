---
title: postgres-array-operator
date: 2019-11-27 19:59:29
tags:
---

||

元素与数组连接

 select 3 || ARRAY[4,5,6];

 {3,4,5,6}

||

数组与元素连接

 select ARRAY[4,5,6] || 7;

 {4,5,6,7}

