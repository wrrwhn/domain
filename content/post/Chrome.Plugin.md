---
title: "Chrome.Plugin"
date: "2017-06-28"
categories:
 - "整理"
tags:
 - "Chrome"
 - "Plugin"
toc: true
---

# 参考
- [Getting Started: Building a Chrome Extension](https://developer.chrome.com/extensions/getstarted)
- [Overview](https://developer.chrome.com/extensions/overview)
- [Developer's Guide](https://developer.chrome.com/extensions/devguide)
- [Sample Extensions](https://developer.chrome.com/extensions/samples)

# 示例解析
- 目录结构
    ```
    html
        setting.html    // 弹出界面
    images
        icon.png        // banner 上小图标，推荐使用 19*19 的半透明 png 图片，同时提供 38*38 的半透明 png 图片作为大图标
    js
        setting.js      // 弹出界面所引用 js 文件，__要求 js 和 html 分开存放__
    manifest.json   // 插件配置文件
    ```

- manifest.json
    ```
    {
      "manifest_version": 2,        // 配置文件版本

      "name": "插件名称",
      "description": "插件功能描述",
      "version": "0.1",             // 插件当前开发版本

      // 通用型
      "browser_action": {           // 浏览器级动作
        "default_icon": "/images/icon.png",         // 默认 banner 上显示图标
        "default_popup": "/html/setting.html"       // 默认点击小图标后弹出界面
      },
      "permissions": [              // 指定权限，如实现功能，可访问站点等
        "activeTab"
      ],

      // 页面型   
      "background": {               // 插件主程序
        "scripts": ["background.js"]
        },       
      "page_action": {              // 界面级动作，限定 permissions= tabs
        "default_icon": {
          "19": "cnblogs_19.png",
          "38": "cnblogs_38.png"
        },
        "default_title": "cnblogs.com article information"
      },
      "permissions": [
        "tabs"
      ],


      "offline_enabled": false
    }
    ```
