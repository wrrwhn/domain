---
title: "扫描二维码自动连接WIFI"
date: "2018-02-10"
categories:
 - "阅读"
tags:
 - "二维码"
 - "WIFI"
toc: true
---


# 扫描二维码自动连接WIFI

## 参考
- [想更优雅地分享 Wi-Fi 密码，只需一枚二维码](https://sspai.com/post/43097)
- [Wifi Network config](https://github.com/zxing/zxing/wiki/Barcode-Contents#wifi-network-config-android)


## 场景
- 手机扫描二维码，自动弹出「连接 WI-FI」建议

## 连接指令
- `WIFI:T:【加密类型】;S:【Wi-Fi 名称】;P:【你的密码】;;`
	- 例
		- `WIFI:T:WPA2;S:Z905;P:nd123456;;`
- 指定协议支持，可参考 PC 端的 `mailto:` 指令

## 推荐
### IOS
- 使用 EFQRCode 创建二维码
	- 支持 GIF 等形式


## 注意事项
- 需要支持 **zxing** 提出的「Wifi Network config (Android)」编码形式
	- 支持该类编码的设备，捕获到如上指令时则自动弹出「加入 WI-FI」的建议
- 需要在 WI-FI 覆盖范围内

