+++
date = "2017-09-08T22:07:46+08:00"
title = "右键菜单绑定"
draft = false
tags = ["整理","注册表"]
share = true
+++

# 右键菜单绑定

## 参考
- [Windows右键菜单设置与应用技巧](http://www.cnblogs.com/russellluo/archive/2011/11/25/2263817.html)
- [命令提示符中同时运行多命令](http://www.45it.com/order/200512/3041.htm)

## 右键上传
- 修改注册表
    - 结构
        - `HKEY_CLASSES_ROOT`
            - `*`
                - `shell`
                    - `（╯‵□′）╯︵┴─┴`
                        - 新增「项」，其中名称为右键菜单要展示的名称
                        - `command`
                            - 新增「项」，对应点击后执行的内容
                                - `默认`
                                    - `cmd.exe /k upload.exe -path=%1 && exit`
                                        - `cmd.exe /k XXXXX`
                                            - 在 cmd 中执行 `XXXXX` 指令
                                            - `&&` 于前一条指令执行完成后执行
    - 示例
        - ![微信图片_20170908170222.png](http://otzm88f21.bkt.clouddn.com/425eca95-80af-4988-97fc-a1676e190dd4.png)
        - ![2.png](http://otzm88f21.bkt.clouddn.com/0a666895-6f16-4dc8-8a4b-5493713fc25f.png)
