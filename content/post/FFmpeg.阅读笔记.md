+++
date = "2017-02-20T22:07:46+08:00"
title = "FFmpeg.阅读笔记"
draft = false
tags = ["整理","FFmpeg"]
share = true
+++

[TOC]

## 简介
- [FFmpeg](https://ffmpeg.org/)
- [FFmpeg - wiki](https://zh.wikipedia.org/wiki/FFmpeg)
- [FFmpeg - github](https://github.com/FFmpeg/FFmpeg)

## 实例
### 裁剪
- 指令
  + `ffmpeg -i old.mp3 -ss 56.808 -write_xing 0 new.mp3`
- 解析
  + `-i old.mp3`
    * 来源
  + `-ss 56.808`
    * 起始时间点，单位秒
  + `-t 999`
    * 结束时间，单位秒
  + `-write_xing 0`
    * 微信-IOS 音频支持

### 转 MP4
- 指令
  + `ffmpeg -i old.video -vcodec libx264 -r 24 -s 640x360 -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y new.mp4`
- 解析
  + `-vcodec libx264`
    * 设置视频视频编解码器，未设置时则使用与输入文件相同之编解码器
  + `-r 24`
    * 每秒帧数 frames per second，默认25
  + `-s 640x360`
    * 设置帧尺寸
      - WXH 与来源一致
      - 640x360 指定尺寸
  + `-acodec libmp3lame`
    * 设置音频编解码器
  + `-ar 22050`
    * 设置音频采样频率,默认24000
  + `-ab 24k `
    * 设置声音比特率，32/ 64/ 96/ 128
  + `-y`
    * 覆盖输出文件

###
- 指令
  + `ffmpeg -i old.audio -vn -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y new.audio`
- 解析
  + `-vn`
    * 禁止视频记录