---
title: "Go.GoSublime"
date: "2017-04-26"
categories:
 - "整理"
tags:
 - "Go"
toc: true
---


## 注解
- Path
    + 分隔符
        * Windows - `;`
        * Linux| OS X - `:`

## 设置
- 路径
    + `Packages/User/GoSublime.sublime-settings`
        * 推荐更新覆盖
    + `Packages/GoSublime/GoSublime.sublime-settings`
        * 默认配置，不建议更新
- 代码补全
    + 配置 `Preferences/Package Settings/GoSublime/Settings - User`
    ```
    {
      "autocomplete_builtins": true,
      "autocomplete_closures": true
    }
    ```
    + 快捷键
        * Code Complete
            - `Ctrl+ [Space]`
        * package import
            - `Ctrl+[.], Ctrl+ P`
        * jump
            - `F12`
            - `Crl+ [.], Ctrl+ i`

## 项目定制化配置
- project.sublime-project
    ```
    {
        "settings": {
            "GoSublime": {
                "env": {
                    "GOPATH": "$HOME/my-project"        // 覆盖 `GoSublime.sublime-settings`
                }
            }
        },
        "folders": []
    }
    ```
