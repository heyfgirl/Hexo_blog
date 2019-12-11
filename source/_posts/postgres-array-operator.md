---
title: postgres对Array的操作
date: 2019-11-27 19:59:29
tags:
---

操作符 | 描述 |  示例 | 结果
-| :-| :-| :-
sf|sfs|sdf|sdfd
= | 相等 | SELECT ARRAY[1.1,2.1,3.1]::int[] = ARRAY[1,2,3]; | true
<> | 不等于 | select ARRAY[1,2,3] <> ARRAY[1,2,4];| true
< |小于| select ARRAY[1,2,3] < ARRAY[1,2,4];| t
> |大于| select ARRAY[1,4,3] > ARRAY[1,2,4];| t
<=|小于或等于| select ARRAY[1,2,3] <= ARRAY[1,2,3];| t
>=|大于或等于| select ARRAY[1,4,3] >= ARRAY[1,4,3];| t
@>|包含| select ARRAY[1,4,3] @> ARRAY[3,1];| t
<@|包含于| select ARRAY[2,7] <@ ARRAY[1,7,4,2,6];|t
&&|重叠（是否有相同元素）| select ARRAY[1,4,3] && ARRAY[2,1];| t
 &#124;&#124; | 数组与数组连接| select ARRAY[1,2,3]&#124;&#124;ARRAY[4,5,6];| {1,2,3,4,5,6}
 &#124;&#124; | 数组与数组连接| select ARRAY[1,2,3]&#124;&#124;ARRAY[[4,5,6],[7,8,9]]; | {1,2,3},{4,5,6},{7,8,9}
 &#124;&#124; | 元素与数组连接 | select 3  &#124;&#124;  ARRAY[4,5,6]; | {3,4,5,6}
 &#124;&#124; | 数组与元素连接| select ARRAY[4,5,6] &#124;&#124; 7; | {4,5,6,7}



[postgresql数组操作符与函数](https://blog.csdn.net/pg_hgdb/article/details/79483767)

postgres to_number(text,text2)  //text2 = 999999.99 为保留两位有效数据，不然将不识别小数点，只认识数字类型
select cast("extension"#>>'{lat}' as double precision) as lat, cast("extension"#>>'{lng}' as numeric) as lng from ads where id=6;
