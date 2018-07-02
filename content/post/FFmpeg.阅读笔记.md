---
title: "FFmpeg.阅读笔记"
date: "2017-11-23"
categories:
 - "整理"
tags:
 - "FFmpeg"
toc: true
---


## 简介
- [FFmpeg](https://ffmpeg.org/)
- [ffmpeg](https://www.ffmpeg.org/ffmpeg.html)
- [FFmpeg Formats Documentation](https://ffmpeg.org/ffmpeg-formats.html)
- [FFmpeg - wiki](https://zh.wikipedia.org/wiki/FFmpeg)
- [FFmpeg - github](https://github.com/FFmpeg/FFmpeg)
- [使用ffmpeg将视频转ts](https://segmentfault.com/q/1010000003834362?sort=created)

## 概要
- `ffmpeg [global_options] {[input_file_options] -i input_url} ... {[output_file_options] output_url} ...`

## 流程
```
 _______              ______________
|       |            |              |
| input |  demuxer   | encoded data |   decoder
| file  | ---------> | packets      | -----+
|_______|            |______________|      |
                                           v
                                       _________
                                      |         |
                                      | decoded |
                                      | frames  |
                                      |_________|
 ________             ______________       |
|        |           |              |      |
| output | <-------- | encoded data | <----+
| file   |   muxer   | packets      |   encoder
|________|           |______________|

```

## 选项


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

### 音频解码
- 指令
  + `ffmpeg -i old.audio -vn -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y new.audio`
- 解析
  + `-vn`
    * 禁止视频记录

### MP4 转 HLS
- 指令
  - `ffmpeg -i origin.wmv -codec:v libx264 -codec:a mp3 -map 0 -f ssegment -segment_format mpegts -segment_list ./m3u8/index.m3u8 -segment_time 10 ./m3u8/’%03d.ts’`
- 解析
  - `-codec:v libx264`
    - 针对视频使用 H.264/MPEG-4 AVC encoder
  - `-codec:a mp3`
    - 针对音频使用 mp3 编码器
  - `-map 0`
    - 指定你所要从输入流中 选择或拷贝 到输出流中的片段
    - -map [-]input_file_id[:stream_specifier][?][,sync_file_id[:stream_specifier]] | [linklabel] (output)
    - 如 `-map 1:0 -map 1:1` 表示将第二个输入文件的第一个流和第二个流写入输出文件
  - `-f ssegment`
    - 强制使用 `ssegment` 格式
  - `-segment_format mpegts`
    - 重置内部容器格式，默认通过文件后缀自动选定
  - `-segment_list ./m3u8/index.m3u8`
    - 生成指定名称的列表目录信息文件，默认不生成
  - `-segment_time 10 './m3u8/’%03d.ts'`
    - 指定分割片段的时长，必须为延续时间段
    - 默认值为 2 秒，且分割不一定是精确的，除非给定指定时间上的参照流的关键帧

### 定时请求代理
- 指令
  - `ffmpeg -re -i big.mp3 -f mp3 http://rtmp.com/proxy/dc6af2ec`
- 解析
  - `-re`
    - 以本机帧速率来读取输入
  - `-f mp3`
    - 强制输入或输出的文件格式
  - `http://rtmp.com/proxy/dc6af2ec`
    - 代理请求接口


### M3U8 转 WEBM
- 指令
  - `ffmpeg -i index.m3u8 -vcodec libvpx -acodec vorbis -strict -2 -ac 2 -ar 22050 -ab 24k -y common.webm`

### M3U8 转 MP3
- 指令
  - `ffmpeg -i index.m3u8 -vn -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y common.mp3`

### M3U8 转 OGG
- 指令
  - `ffmpeg -i index.m3u8 -vn -acodec vorbis -strict -2 -ac 2 -ar 22050 -ab 24k -y common.ogg`

### M3U8 转 MP4
- 指令
  - `ffmpeg -i index.m3u8 -codec copy -y common.mp4`    