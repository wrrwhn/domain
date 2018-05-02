+++
date = "2017-09-12T22:07:46+08:00"
title = "Error.Mysql"
draft = false
tags = ["整理","Error","MYSQL"]
share = true
+++


## 1175
- Description
     - `You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column To disable safe mode, toggle the option in Preferences -> SQL Editor and reconnect.`
- Answer
     - `SET SQL_SAFE_UPDATES = 0;`
