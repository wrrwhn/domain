---
title: "Hello.FFmpeg"
date: "2018-07-10"
categories:
 - "整理"
tags:
 - "FFmpeg"
toc: true
---



# 概要
- `ffmpeg [global_options] {[input_file_options] -i input_url} ... {[output_file_options] output_url} ...`


# 流程
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


# 示例

## 图片.*
### 截图
- `ffmpeg -i dest/png_mp3.mp4 dest/png_mp3-mp4/%d.jpg`
    ```
    **必须** 先创建[dest/png_mp3-mp4]文件夹
    ```

### 动态图
- `ffmpeg -i video/bike.mp4 -ss 0 -t 2 -vf "fps=15,scale=320:-1" -y dest/bike-vf.gif`
    ```
    file[fps=15,scale=320:-1].size= 825K
    ```

- `ffmpeg -i video/bike.mp4 -ss 0 -t 2 -r 24 -s 320:260 -y dest/bike-rs.gif`
    ```
    file[-s 320:260].size= 2,252K
    file[-r 24 -s 320:260].size= 1,824K
    ```    

## 视频.*
### 格式转换
+ `ffmpeg -i old.video -vcodec libx264 -r 24 -s 640x360 -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y new.mp4`
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

### 转 M3U8        
- `ffmpeg -i origin.wmv -codec:v libx264 -codec:a mp3 -map 0 -f ssegment -segment_format mpegts -segment_list ./m3u8/index.m3u8 -segment_time 10 ./m3u8/’%04d.ts’`
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
    - `-segment_time 10 './m3u8/’%4d.ts'`
        - 指定分割片段的时长，必须为延续时间段
        - 默认值为 2 秒，且分割不一定是精确的，除非给定指定时间上的参照流的关键帧

### M3U8 转
- 常用
  - `ffmpeg -i index.m3u8 [-c copy -bsf:a aac_adtstoasc] -y output.mp4`
      ```sh
      Result
          ts.time:    603.386| 10:03
          mp4.time:   04:50.55
          **实际时长与转换后时长不一致**
              部分情况下出现

      Error
          Non-monotonous DTS in output stream 0:0; previous: 26142599, current: 24141600; changing to 26142600. This may result in incorrect timestamps in the output file.
          ResultDuration is less than real duration

      Duration
          ffmpeg -i index.m3u8 -y output.mp4
              1:53.81
          ffmpeg -i index.m3u8 -c copy -bsf:a aac_adtstoasc -y output.mp4
              0:05.51
      ```

- 优化
  - 针对常用可能出现的 TS 片长度与 m3u8 文件中记录存在偏差，导致合成时长不足的异常
  - `ffmpeg -i all.ts -codec copy -y output.mp4`
      - `ls -v *.ts | grep "[0-9]" | xargs cat > all.ts`

  - `ffmpeg -f concat -safe 0 -i list -codec copy -y output.mp4`
    - `cat index.m3u8 | grep ".ts" | awk '{printf "file %s\n", $0}' > list`
    - 速度较 all.ts 慢差不多一倍，具体统计可见 [补充 - all.ts 与 ts.list 方案对比](https://github.com/yqjdcyy/Hello_Ffmpeg/blob/master/README.md#%E6%AF%94%E8%BE%83)

- 其它格式
  - `ffmpeg -i index.m3u8 -vcodec libvpx -acodec vorbis -strict -2 -ac 2 -ar 22050 -ab 24k -y common.webm`
  - `ffmpeg -i index.m3u8 -vn -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y common.mp3`
  - `ffmpeg -i index.m3u8 -vn -acodec vorbis -strict -2 -ac 2 -ar 22050 -ab 24k -y common.ogg`
  - `ffmpeg -i index.m3u8 -codec copy -y common.mp4`


### 图音合成
- `ffmpeg -i img/bg.png -i audio/bgm-min.mp3 [-acodec copy] -y dest/png_mp3.mp4`
- `ffmpeg -i dest/png_mp3_loop-mp4/%d.jpg -i audio/bgm-min.mp3 -y dest/jpgs_mp3.mp4`
- `ffmpeg -i img/loop/%d.png -r 24 -i audio/bgm-min.mp3 -y dest/pngs_r24.mp4`
    ```
    imgs(25)+ audio(3s)= video(3s)
    ```

- `ffmpeg -framerate 6 -i img/loop/%d.png -i audio/bgm-min.mp3 -y dest/pngs_framerate6.mp4`
    ```
    imgs(25)+ audio(3s)= video(4s, 6 frame/second)
    ```


## 音频.*
### 解码
+ `ffmpeg -i old.audio -vn -acodec libmp3lame -write_xing 0 -ar 22050 -ab 24k -y new.audio`
    + `-vn`
        * 禁止视频记录


## 其它
### 裁剪
- `ffmpeg -i old.mp3 -ss 56.808 -t 999 -write_xing 0 new.mp3`
    - `-i old.mp3`
      - 来源
    - `-ss 56.808`
      - 起始时间点，单位秒
    - `-t 999`
      - 结束时间，单位秒
    - `-write_xing 0`
      - 微信-IOS 音频支持

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


# 参考
## 官网
- [ffmpeg](https://www.ffmpeg.org/ffmpeg.html)
- [FFmpeg Formats Documentation](https://ffmpeg.org/ffmpeg-formats.html)
- [FFmpeg - wiki](https://zh.wikipedia.org/wiki/FFmpeg)
- [FFmpeg - github](https://github.com/FFmpeg/FFmpeg)

## IMG-> Video
- [Combine one image + one audio file to make one video using FFmpeg](https://superuser.com/questions/1041816/combine-one-image-one-audio-file-to-make-one-video-using-ffmpeg)
- [Useful ‘FFmpeg’ Commands for Video, Audio and Image ](https://www.tecmint.com/ffmpeg-commands-for-video-audio-and-image-conversion-in-linux/)
- [How do I convert a video to GIF using ffmpeg, with reasonable quality?](https://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality)


## M3U8-> Video
- [FFMPEG mp4 from http live streaming m3u8 file?](https://stackoverflow.com/questions/32528595/ffmpeg-mp4-from-http-live-streaming-m3u8-file)
- [Proper command to convert m3u8 to mp4](https://apple.stackexchange.com/questions/285635/proper-command-to-convert-m3u8-to-mp4)
- [利用ffmpeg合併m3u8串流影片，並且轉成MP4格式](https://shimeche.github.io/2017/04/13/%E5%88%A9%E7%94%A8ffmpeg%E5%90%88%E4%BD%B5m3u8%E4%B8%B2%E6%B5%81%E5%BD%B1%E7%89%87%EF%BC%8C%E4%B8%A6%E4%B8%94%E8%BD%89%E6%88%90MP4%E6%A0%BC%E5%BC%8F/)


## 其它
- [使用ffmpeg将视频转ts](https://segmentfault.com/q/1010000003834362?sort=created)  


## 细节
- [yqjdcyy/Hello_Ffmpeg](https://github.com/yqjdcyy/Hello_Ffmpeg)