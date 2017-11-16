+++
date = "2017-11-16T22:07:46+08:00"
title = "ImageMagick"
draft = false
tags = ["阅读","ImageMagick"]
share = true
+++


[TOC]


# ImageMagick

## Reference
- [官网](http://www.imagemagick.org/script/command-line-processing.php)


## Anatomy
- convert|composite|montage|compare|import|conjure [input filenames] [image settings] [image operators] [sequence operators] [stacks] [output image filenames]

### Input FileNames
#### FileName Globbing
- Analysis
    - pattern match about */?
- Example
    - convert *.jpg images.gif
#### Explicit Image Format
- Analysis
    - specified a explicit image format is better than in the case of the image donot contain a signature of identify format
- Example
    - convert -size 640x480 -depth 8 rgb:image image.png
#### Built-in Image and Pattern
- Analysis
    - you can use a number of built-in image/ [patterns](http://www.imagemagick.org/script/formats.php#builtin-patterns) to build image
- Example
    - convert -size 640x480 pattern:checkerboard checkerboard.png
#### STDIN·STDOUT·File Descriptors
- Analysis
    - output could be piped to the input
    - fd:[0/1/2]     -> STDIN/ STDOUT/ STDERR
    - fd:N(N>2)     -> pseudonym for file
- Example
    - convert rose: gif:- | convert - -resize "200%" bigrose.jpg'
        - ! no work for me in linux
#### Selecting Frames
- Analysis
    - deal with the image which has more than one frames
    - Unix shells generally interpret brackets so we enclosed the filename in quotes above
- Example
    - convert 'images.gif[0]' image.png
    - convert 'images.gif[-1]' image.png
        - last frame
    - convert 'images.gif[0-3]' image.png
        - stash into image-[0-3].png
    - convert 'images.gif[3,2,3]' image.mng
        - .mng support for multi frames
#### Selecting an Image Region
- Analysis
    - select a part region of the origin image as a new one
- Example
    - convert -size 100x100 'hexagons.png[50x50+25+25]' hexagon-50x50.png
    - convert 'hexagons.png[30x30+35+35]' hexagon-30x30.png
#### Inline Image Resize
- Analysis
    - resize iamge
- Example
    - convert mid-frame-0.png -resize 100x100 mid-frame-0-resize-100x100.png
    - convert 'mid-frame-0.png[1000x1000]' mid-frame-0-resize-1000x1000.png
#### Inline Image Crop
- Analysis
    - crop the region of image
- Example
    - convert '*.jpg[120x120+10+5]' thumbnail%03d.png
#### Filename References
- Analysis
    - user file or pattern to stash filenames
- Example
    - convert `@mid-frame.txt` mid-frame.gif
        - frame001.jpg
        - frame002.jpg
        - frame003.jpg
    - convert `image-%d.jpg[1-5]`
        - image-1.jpg
        - image-2.jpg
        - image-3.jpg
        - image-4.jpg
        - image-5.jpg
#### Stream Buffering
- Analysis
    -
- Example
    - convert logo: gif:- | display -define stream:buffer-size=0 gif:-

### Command-line Options
#### ImageSetting
- adjoin
     - Analysis
          - for save a sequence images
     - Example
          - convert logo: rose: -morph 15 my`%02d`morph.jpg
              - my00morph.jpg
              - .
              - my16morph.jpg
- affine
     - Analysis
          - 仿射
     - Example
          -
- alpha
     - Analysis
          - α
     - Example
          -
- alpha color
     - Analysis
          - 阿尔法颜色
     - Example
          -
- antialias
     - Analysis
          - 反锯齿
     - Example
          -
- authenticate
     - Analysis
          - 认证
     - Example
          -
- background
     - Analysis
          - 背景
     - Example
          -
- bias
     - Analysis
          - 偏压
     - Example
          -
- black point compensation
     - Analysis
          - 黑点补偿
     - Example
          -
- blue primary
     - Analysis
          - 蓝色三原色
     - Example
          -
- border color
     - Analysis
          - 边框颜色
     - Example
          -
- caption
     - Analysis
          - 标题
     - Example
          -
- channel
     - Analysis
          - 渠道
     - Example
          -
- comment
     - Analysis
          - 评论
     - Example
          -
- compress
     - Analysis
          - 压缩
     - Example
          -
- debug
     - Analysis
          - 调试
     - Example
          -
- define
     - Analysis
          - 确定
     - Example
          -
- delay
     - Analysis
          - 延迟
     - Example
          -
- density
     - Analysis
          - 密度
     - Example
          -
- depth
     - Analysis
          - 深度
     - Example
          -
- direction
     - Analysis
          - 方向
     - Example
          -
- display
     - Analysis
          - 显示
     - Example
          -
- dispose
     - Analysis
          - 部署
     - Example
          -
- dither
     - Analysis
          - 抖动
     - Example
          -
- encoding
     - Analysis
          - 编码
     - Example
          -
- endian
     - Analysis
          - 尾数
     - Example
          -
- extract
     - Analysis
          - 提取
     - Example
          -
- family
     - Analysis
          - 家庭
     - Example
          -
- fill
     - Analysis
          - 填
     - Example
          -
- filter
     - Analysis
          - 过滤
     - Example
          -
- font
     - Analysis
          - 字形
     - Example
          -
- format
     - Analysis
          - 格式
     - Example
          -
- fuzz
     - Analysis
          - 模糊
     - Example
          -
- geometry
     - Analysis
          - 几何
     - Example
          -
- gravity
     - Analysis
          - 重力
     - Example
          -
- green primary
     - Analysis
          - 绿色基色
     - Example
          -
- interlace
     - Analysis
          - 交错
     - Example
          -
- intent
     - Analysis
          - 意图
     - Example
          -
- interpolate
     - Analysis
          - 插
     - Example
          -
- label
     - Analysis
          - 标签
     - Example
          -
- limit
     - Analysis
          - 限制
     - Example
          -
- linewidth
     - Analysis
          - 行宽
     - Example
          -
- log
     - Analysis
          - 日志
     - Example
          -
- loop
     - Analysis
          - 循环
     - Example
          -
- mask
     - Analysis
          - 面具
     - Example
          -
- matte color
     - Analysis
          - 亚光色
     - Example
          -
- monitor
     - Analysis
          - 监控
     - Example
          -
- orient
     - Analysis
          - 东方
     - Example
          -
- page
     - Analysis
          - 页
     - Example
          -
- pointsize
     - Analysis
          - 的pointsize
     - Example
          -
- preview
     - Analysis
          - 预习
     - Example
          -
- quality
     - Analysis
          - 质量
     - Example
          -
- quiet
     - Analysis
          - 安静
     - Example
          -
- red primary
     - Analysis
          - 红原
     - Example
          -
- region
     - Analysis
          - 地区
     - Example
          -
- render
     - Analysis
          - 给予
     - Example
          -
- repage
     - Analysis
          - repage
     - Example
          -
- sampling factor
     - Analysis
          - 采样因素
     - Example
          -
- scene
     - Analysis
          - 现场
     - Example
          -
- seed
     - Analysis
          - 种子
     - Example
          -
- size
     - Analysis
          - 尺寸
     - Example
          -
- stretch
     - Analysis
          - 伸展
     - Example
          -
- stroke
     - Analysis
          - 行程
     - Example
          -
- stroke width
     - Analysis
          - 笔划宽度
     - Example
          -
- style
     - Analysis
          - 样式
     - Example
          -
- texture
     - Analysis
          - 质地
     - Example
          -
- tile
     - Analysis
          - 瓦
     - Example
          -
- transparent color
     - Analysis
          - 透明色
     - Example
          -
- tree depth
     - Analysis
          - 树的深度
     - Example
          -
- type
     - Analysis
          - 类型
     - Example
          -
- undercolor
     - Analysis
          - 底色
     - Example
          -
- units
     - Analysis
          - 单位
     - Example
          -
- verbose
     - Analysis
          - 详细
     - Example
          -
- virtual pixel
     - Analysis
          - 虚拟像素
     - Example
          -
- weight
     - Analysis
          - 重量
     - Example
          -
#### Image Operator
#### Image Sequence Operator
#### Image Geometry
#### Image Stack


composite -tile /data/cdn/resource/repeat.png /data/tmp/yao/from/yk-white.jpg jpg:- | convert - "/data/cdn/resource/logo.png" -gravity northeast -geometry +24+20 -composite jpg:- | convert -quality 75 - /data/tmp/yao/to/preview-yk-white.jpg





convert -size 140x80 xc:none -fill grey -gravity NorthWest -draw "text 10,10 'live.yunkai.com'" -gravity SouthEast -draw \"text 5,15 'live.yunkai.com'\" miff:- | composite  -tile - resource/part.png to/test-image.png
composite -tile resource/part.png from/slide2.jpg to/slide2-tile-part.jpg | convert - -gravity southeast -fill white -pointsize 16 -draw "text 5,5 'http://live.yunkai.com'" slide2_word_en.jpg
convert slide2-tile-part.jpg -gravity southeast -fill white -pointsize 16 -draw "text 5,5 'http://live.yunkai.com'" slide2_word_en.jpg



composite -tile resource/repeat-2.png from/slide2.jpg slide2-tile-repeat.jpg
convert from/slide2.jpg resource/logo.png -scale 300% -gravity northeast -composite slide2-logo.jpg


/usr/local/bin/composite -tile /data/tmp/yao/resource/repeat-4.png /data/tmp/yao/from/slide2.jpg jpg:- | /usr/local/bin/convert - "/data/tmp/yao/resource/logo-2.png[182x27]" -gravity northeast -geometry +5+10 -composite jpg:- | /usr/local/bin/convert -quality 75 - /data/tmp/yao/tile-logo-quality-2.jpg

/usr/local/bin/composite -tile /data/tmp/yao/resource/repeat-4.png /data/tmp/yao/from/slide-rId4.jpg jpg:- | /usr/local/bin/convert - "/data/tmp/yao/resource/logo-2.png[182x27]" -gravity northeast -geometry +5+10 -composite jpg:- | /usr/local/bin/convert -quality 75 - /data/tmp/yao/to/slide-rId4.jpg

/usr/local/bin/convert -quality 75 /data/cdn/dev/img/0c678b1d-1a03-40e3-84dd-870d7d557d04.jpg /data/cdn/dev/img/0c678b1d-1a03-40e3-84dd-870d7d557d04.jpg


/usr/local/bin/composite -tile /data/tmp/yao/resource/repeat-4.png /data/tmp/yao/from/slide-rId4.jpg jpg:- | /usr/local/bin/convert - "/data/tmp/yao/resource/logo-2.png[182x27]" -gravity northeast -geometry +5+10 -composite jpg:- | /usr/local/bin/convert -quality 75 - /data/tmp/yao/to/slide-rId4.jpg
