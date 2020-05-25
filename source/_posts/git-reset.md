---
title: git回滚操作
date: 2020-04-24 15:13:05
tags:
---
1.暴力重置
先gitreset 到该分支之前某个节点
git reset --hard HEAD
然后进行强制推送到远程处理
git push -u origin master -f       
origin：远程仓库名  master：分支名称  -f：force，意为强制、强行

2.覆盖恢复
git revert -n branch 覆盖到之前某个节点
git commit -m "revert add text.txt"  提交信息
git push    推送到远程