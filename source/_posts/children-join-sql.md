---
title: children_join_sql
date: 2020-06-08 17:02:10
tags:
---
SELECT "id", "subs", "name", "sort", "level", "state", 
(SELECT cast(COUNT(*) as integer) AS "count"  FROM "goods" AS "goods" WHERE "goods"."deletedAt" IS NULL AND "goods"."state" = 1 AND "goods"."classes" && ARRAY["class"."id"]::INTEGER[]) AS "goods_count" 
FROM "classes" AS "class" 
WHERE (("class"."deletedAt" > '2020-06-08 15:55:25.728 +08:00' OR "class"."deletedAt" IS NULL) AND ("class"."level" = 1 AND "class"."state" = 1 AND "goods_count">0)) ORDER BY "class"."sort" ASC, "class"."id" DESC;

【AND "goods_count">0】
上面查询sql语句想查询出class表中信息并且查询出其与goods数据表关联字段的信息
上述查询会出现报错，因为 子查询中使用了父查询中的检索信息，使得父查询的生命周期高于子查询，此时父查询中无法查询子查询的字段数据。

改为下列请求


SELECT "class"."id", "class"."subs", "class"."name", "class"."sort", "class"."level", "class"."state", cast(COUNT("goods"."id") as integer) AS "count"
FROM "classes" AS "class" RIGHT OUTER JOIN goods on ("goods"."deletedAt" IS NULL AND "goods"."state" = 1 AND "goods"."classes" && ARRAY["class"."id"]::INTEGER[])
WHERE (("class"."deletedAt" > '2020-06-08 15:55:25.728 +08:00' OR "class"."deletedAt" IS NULL) AND ("class"."level" = 1 AND "class"."state" = 1)) 
GROUP BY "class"."id"
ORDER BY "class"."sort" ASC, "class"."id" DESC ;


或者在父查询中再次使用联合检索数据

SELECT "id", "subs", "name", "sort", "level", "state", 
(SELECT cast(COUNT(*) as integer) AS "count"  FROM "goods" AS "goods" WHERE "goods"."deletedAt" IS NULL AND "goods"."state" = 1 AND "goods"."classes" && ARRAY["class"."id"]::INTEGER[]) AS "goods_count" 
FROM "classes" AS "class" 
WHERE (("class"."deletedAt" > '2020-06-08 15:55:25.728 +08:00' OR "class"."deletedAt" IS NULL) AND ("class"."level" = 1 AND "class"."state" = 1 AND (SELECT cast(COUNT(*) as integer) AS "count"  FROM "goods" AS "goods" WHERE "goods"."deletedAt" IS NULL AND "goods"."state" = 1 AND "goods"."classes" && ARRAY["class"."id"]::INTEGER[])>0)) ORDER BY "class"."sort" ASC, "class"."id" DESC;