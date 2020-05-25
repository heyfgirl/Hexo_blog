---
title: postgresql对jsonb类型字段操作
date: 2020-04-24 15:01:43
tags:
---
https://elemefe.github.io/
SELECT (extension ::jsonb->> 'sex')::jsonb->>'sex' FROM "accounts" WHERE extension ::jsonb->> 'sex' is not null