+++
date = "2017-09-13T22:07:46+08:00"
title = "Wechat.Export"
draft = false
tags = ["整理","Wechat"]
share = true
+++

[TOC]

# 微信语音导出

## 参考
-[如何导出微信【收藏】中的语音文件？](https://www.zhihu.com/question/30112442)
-[Windows下批量转换Silk v3音频文件为MP3格式](https://kn007.net/topics/batch-convert-silk-v3-audio-files-to-mp3-in-windows/)
    -[silk2mp3.zip](http://dl.kn007.net/directlink/silk2mp3.zip)
        - 3/3
-[ONLINE AUDIO CONVERTER](http://media.io/)
    - 0/ 1
-[kn007/silk-v3-decoder](https://github.com/kn007/silk-v3-decoder/tree/master/windows)


## 流程
- 将语音收藏
- 在手机*完整收听*
- 使用目录管理搜索提取
    - *./sdcard/tecent/MicroMsg/*
    - 搜索 *.silk* 文件
- 拷贝至电脑
- 使用 silk2mp3 工具将音频*转换*为 mp3 格式文件

## 注意
- SILK v3
    - 为 skype 向第三方开发人员或硬件制造商提供的免版税谁的 Silk 宽音频编码器
    - 文件更为精简
        - xxx.silk     18KB
        - xxx.mp3     189KB
