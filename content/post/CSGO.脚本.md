---
title: "CSGO.脚本"
date: "2018-03-15"
categories:
 - "整理"
tags:
 - "游戏"
 - "CSGO"
 - "脚本"
toc: true
---


# 设置项
- [CSGO详细参数.md](http://doc.yqjdcyy.com/c53affd5-4640-4464-9a64-af36de605fa3.md)

# 脚本
## 示例
- 单人模式
	- single.cfg
	```
	sv_cheats 1
	bot_kick
	sv_infinite_ammo 1
	noclip
	bind "H" "noclip"
	mp_startmoney "16000"
	sv_grenade_trajectory "1"
	sv_grenade_trajectory_time "20"
	sv_grenade_trajectory_dash "0"
	sv_grenade_trajectory_thickness "1"
	sv_grenade_trajectory_time_spectator "1"
	sv_showimpacts 1
	sv_showimpacts_time 25
	mp_roundtime 99
	mp_timelimit "999"
	mp_freezetime "0"
	mp_buy_anywhere "1"
	mp_buytime 9999
	bind "k" "bot_add_ct"
	bind "l" "bot_add_t"
	bot_zombie "0"
	```
	- 备注
		- 目前尝试无效项
		- 冻结时间取消
		- 金钱设置
		- 回合时长

## 开启开发者控制台
- 「游戏设置选项」
	- 「游戏设置」
		- 「启用开发者控制台」

## 执行
- 将 single.cfg 拷贝至 `Steam\steamapps\common\Counter-Strike Global Offensive\csgo\cfg` 文件夹下
- 开启游戏时，在命令行中输入的执行 `exec single.cfg`


# 参考
- [CS:GO一鍵買槍，常規參數，網絡參數配置文件](https://steamcn.com/t66337-1-1)
- [CSGO常用脚本及设置方法（附打包下载](https://tieba.baidu.com/p/4015701229)
- [CSGO 单机测试指令](http://steamcommunity.com/sharedfiles/filedetails/?id=426703776)
- [CSGO怎么调出控制台?](http://fight.pcgames.com.cn/366/3663120.html)
