---
title: "Error.MYSQL"
date: "2017-09-12"
categories:
 - "整理"
tags:
 - "Error"
 - "MYSQL"
toc: true
---


## 1175
- Description
     - `You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column To disable safe mode, toggle the option in Preferences -> SQL Editor and reconnect.`
- Answer
     - `SET SQL_SAFE_UPDATES = 0;`
