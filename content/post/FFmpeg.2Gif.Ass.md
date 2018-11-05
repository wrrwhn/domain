---
title: "FFmpeg.2Gif.Ass"
date: "2018-06-01"
categories:
 - "整理"
tags:
 - "FFmpeg"
toc: true
---



[TOC]

# 字幕文件
## 安装 aegisub
- [aegisub.官网](http://www.aegisub.org/)

## 打开视频
- 注意选择合适大小

## 编辑
- /视频/打开视频
- 试听 & 添加行记录 & 更新时间点、字幕内容和样式
	- ![Aegisub-操作.png](http://doc.yqjdcyy.com/47ae9854-8dc0-46f4-9251-3626284c1491.png)

# 指令
## 模板
- `ffmpeg -i {video_path} -r 8 -vf ass={ass_path},scale=300:-1 -y {gif_path}`

## 示例
- `cd path`
- `ffmpeg -i ‪template.mp4 -r 8 -vf ass=‪template.ass,scale=300:-1 -y template.gif`

## 代码参考
- [sorry.python - render.py](https://github.com/East196/sorrypy/blob/master/render.py)


# Reference
## Project
- [East196/sorrypy](https://github.com/East196/sorrypy)
- [xtyxtyx/sorry](https://github.com/xtyxtyx/sorry)

## Software
- [aegisub](http://www.aegisub.org/)
- [【教程】手把手教你用aegisub写字幕（基础向）](https://tieba.baidu.com/p/1360405931?see_lz=1)