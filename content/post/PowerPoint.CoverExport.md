---
title: "PowerPoint.CoverExport"
date: "2017-11-16"
categories:
 - "整理"
tags:
 - "PowerPoint"
toc: true
---


## 文件法
### 实现
- 将 `.pptx` 文件后缀更新为 `.zip`
- 获取 `/docProps/thumbnail.jpeg`

### 缺点
- 仅适用于 `.pptx`


## windows api + code
### 实现

### 环境要求& 版本限制
- `v4.0.30319`

### 缺点
- 与「文件法」一致，导出图片尺寸过小
    + [PPT 操作导出 - 79KB 1024x766](/50fc4b9a-0e0a-11e7-a1d9-0071cc916200.png)
    + [api 导出 - 24KB 960x720](/57952808-0e0a-11e7-a0ab-0071cc916200.png)
- 部分 PPT 无缩略图，导出的均为 [PPT 默认图片](/2ffdec48-0e10-11e7-96bf-0071cc916200.png)


## OpenXML + code
### 现服务转换方式
- 高清无码

### 环境要求& 版本限制
- `v2.0.50727`
- 安装有 Microsoft.PowerPoint

### 缺点
- 要求本地安装有`指定以上版本 PPT`