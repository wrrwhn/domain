+++
date = "2018-01-18T15:07:46+08:00"
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
#### Image Operator
#### Image Sequence Operator
#### Image Geometry
#### Image Stack


## Example
- 添加全屏水印图
  - `composite -tile /data/cdn/resource/repeat.png /data/tmp/yao/from/yk-white.jpg /data/tmp/yao/from/yk-white-repeat.jpg`
  - ![slide-rId3-blue.jpg](http://otzm88f21.bkt.clouddn.com/c954d58e-276f-40cb-9a00-9cbed0f08e0e.jpg)

- 于指定位置添加水印图
  - `convert slide-rId3.jpg "/data/cdn/resource/logo.png[300x100]" -gravity northeast -geometry +24+20 -composite jpg:- | convert - slide-rId3-logo.jpg`
  - ![slide-rId3-logo.jpg](http://otzm88f21.bkt.clouddn.com/030cbd21-8248-45af-bee2-edd9beb189d4.jpg)

- 添加文字水印
  - 失败
    - `convert -size 140x80 xc:none -fill grey -gravity NorthWest -draw "text 10,10 'live.yunkai.com'" -gravity SouthEast -draw \"text 5,15 'live.yunkai.com'\" miff:- | composite  -tile - slide-rId3-font.jpg slide-rId3-font-watermark.jpg`
    - `convert slide-rId3.jpg -gravity southeast -fill white -pointsize 16 -draw "text 5,5 'http://live.yunkai.com'" slide-rId3-font-watermark.jpg`

- 图片质量调整
  - `convert -quality 75 slide-rId3.jpg slide-rId3-075.jpg`
  - ![slide-rId3.jpg](http://otzm88f21.bkt.clouddn.com/4e978cef-096b-43a4-92d8-14660bd69f15.jpg)
  - ![slide-rId3-075.jpg](http://otzm88f21.bkt.clouddn.com/36ae72dd-5ece-4bc7-8fbe-7154d2183006.jpg)


- 动态图倒放
  - `convert from.gif from/%d.png`
  - `ls -[r]t from | paste -s -d`
  - `convert -delay 10 - all.gif`