---
title: "Hugo 建站"
date: "2017-11-15"
categories:
 - "整理"
tags:
 - "Go"
 - "Hugo"
toc: true
---


# Hugo 建站

## 参考
- [使用Hugo搭建个人博客](http://listenzhangbin.com/post/2016/03/go-hugo-blog/)
- [Hugo Themes](https://themes.gohugo.io/)
- [Hugo 中文文档](http://www.gohugo.org)
- [利用Github Pages和基于Go的Hugo搭建个人博客](http://lucumt.info/posts/create-website-with-hugo/)
- [Host on GitHub](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
- [使用hugo搭建个人博客站点](http://www.gohugo.org/post/coderzh-hugo/)
- [github Pages和Jekyll入门](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
- [3.5. 建立主页](http://www.worldhello.net/gotgithub/03-project-hosting/050-homepage.html)


## 安装
- 下载
	- [gohugoio/hugo](https://github.com/gohugoio/hugo/releases)
- 安装
	- `tar xvf hugo_0.15_linux_amd64.tar.gz -C /data/soft/hugo`

## 生成
- `/data/soft/hugo/hugo new site /data/service/domain`
	- archetypes
	- config.toml
		- 整体配置
	- content
		- 文章
	- data
	- layouts
	- static
	- themes
		- 主题

## 安装主题
- 仅需将主题下载至 themes 文件下，并更 config.toml 中的 theme 为主题名称
	- 下载
		- `git clone https://github.com/yoshiharuyamashita/blackburn.git /data/service/domain/themes/blackburn`
		- hugo-minimalist-theme
	- 更新配置
		- `vim config.toml`
			- `theme = "hugo-minimalist-theme"`
		- 注：其它相关配置详见各主题项目的 README.md 文件

## 发布
- `/data/soft/hugo/hugo`
	- hugo 将配置和文件转换为静态文件发布到本地目录 public 下

## 启动
### Hugo
- `cd /data/service/domain`
- `/data/soft/hugo/hugo server --baseURL=http://www.yqjdcyy.com/ --port=80 --appendPort=false --bind=0.0.0.0`

### Nginx
```
server {
	listen 80;
	server_name 你的域名;
	index index.html;
	root /home/www/public; #这里配置指向根目录
}
```

## github-page
- 将打包文件夹提交至 `gh-pages` 分支，作为项目首页
	- `git subtree push --prefix=public git@github.com:wrrwhn/domain.git gh-pages`

## 域名指向
	- `vim CNAME`
		- `www.yqjdcyy.com`
	- 域名指向
		- 顶级域名
			- A.yqjdcyy.com -> 204.232.175.78
		- 二级域名
			- domain.yqjdcyy.com -> 204.232.175.78
	- 配置更新
		- baseUrl -> "/"