---
title: "Sublime.Plugin"
date: "2016-12-11"
categories:
 - "整理"
tags:
 - "Sublime"
 - "Plugin"
toc: true
---


# 参考
- [sublime插件开发](http://mux.alimama.com/posts/541)


# 结构
- Packages
- plugin-fodler
- plugin.sublime-package(.zip)
- default
- key/ menu/ setup
- user
- read at lastest
- plugin-command
- Text Commands:  current view
- Window Commands:  current windows
- Application Commands: nothing
- 注： sublime 会将继承 plugin-command 的类去掉 Command 后缀，并将驼峰格式转换为下划线格式


# 流程
- Tools-> new plugin...
- mkdir Packages/ hello_world/ hello_world.py： 同名
- ctrl+ ` 并输入 view.run_command('example') -> 即可看到当前文章头部插入 'Hello, World!'

----------------------
# **[sublime插件开发手记](http://blog.hickwu.com/sublime%E6%8F%92%E4%BB%B6%E5%BC%80%E5%8F%91%E6%89%8B%E8%AE%B0)**

# [Sublime插件开发API手册](http://mux.alimama.com/posts/549)
- API
- Sublime
- error/ message
- clipboard.setter& getter
- run_command(string, <args>)
- log_commands/input(flag)
- version/ platform/ arch
- View - 缓冲区视图
- run_command(string, <args>)
- insert/ erase/ replace/ find
- sel()/ rowcol
- show/ fold/ unfold
- run_command
- RegionSet
- Region
- Edit
- Window
- new_file([file_name, <flag>])
- flag
- sublime.ENCODED_POSITION - 查找文件名后缀
- sublime.TRANSIENT - 指定预览打开
- active_view/ focus_view/ views
- Settings - sublime.load_settings
- get/ set/ erase
- Basic
- EventListener
- new/ clone/ load/ close/ preSave/ postSave/ modified/ selectionModified/ activated
- ApplicationCommand
- run
- isEnabled/ isVisible
- description
- WindowCommand
- 每个 window 只初始化一次，方法同 ApplicationCommand
- TextCommand
- 每个 view 只初始化一次，方法同 ApplicationCommand
