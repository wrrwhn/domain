---
title: "License.Choose"
date: "2018-03-30"
categories:
 - "整理"
tags:
 - "License"
toc: true
---


# 作用
计算机软件的版权许可协议，其使源代码可用在允许修改和重分发而不要向原始创建者付费的条件下
开源许可证是一种法律许可。通过它，版权拥有人明确允许，用户可以免费地使用、修改、共享版权软件。
版权法默认禁止共享，也就是说，没有许可证的软件，就等同于保留版权，虽然开源了，用户只能看看源码，不能用，一用就会侵犯版权。所以软件开源的话，必须明确地授予用户开源许可证。

# 简诉
- ![License关系图.png](http://otzm88f21.bkt.clouddn.com/201fb774-e22d-47a7-b047-ee01e95156ec.png)

# 类型
## MIT
- 历史
	- 名源自麻省理工学院（Massachusetts Institute of Technology, MIT），又称「X许可协议」（X License）或「X11许可协议」(X11 License）
- 声明
	```
	The MIT License (MIT)
	Copyright (c) [year] [copyright holders]

	Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

	The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

	THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
	```
- 使用
	- 可以**任意使用、拷贝和修改**软件。
	- 可以免费分发或出售**软件**，分发软件的方式**不受限制**。
	- 唯一的限制是软件**必须包含该许可协议的声明**。
- 项目
	- jQuery
	- Rails
	- Lua
	- PuTTY
	- Bitcoin

## BSD
- 历史
	- 修订版 BSD 许可协议（The BSD 3-Clause License / New BSD License / Modified BSD License)
	- 简版 BSD 许可协议（The BSD 2-Clause License / Simplified BSD License / FreeBSD License）
- 声明
	```
	Copyright (c) [YEAR], [OWNER]
	All rights reserved.

	Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

	Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
	Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
	Neither the name of the copyright holder nor the names of its contributors may be used to endorse or promote products derived from this software without specific prior written permission.
	THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS “AS IS” AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
	```
- 使用
	- 可以**任意使用**、拷贝和修改软件。
	- 可以免费分发软件或者出售软件，分发软件的方式**不受限制**。
	- 软件必须**包含该许可协议的声明**。
	- 未获书面许可，**不能**使用**软件贡献者的名称**来为软件的衍生产品做任何表示支持、认可或推广、**促销**之行为。
		- **BSD3 较 BSD2 多的条款**
- 项目
	- Go
	- OpenSSH
	- V8（JavaScript引擎）
	- Tengine

## GPL
- 历史
	-  GNU **通用**公共许可协议（GNU General Public License）
		- GPL-2.0
		- GPL-3.0
- 声明
	```
	                    GNU GENERAL PUBLIC LICENSE
	                       Version 3, 29 June 2007

	 Copyright (C) 2007 Free Software Foundation, Inc. <http://fsf.org/>
	 Everyone is permitted to copy and distribute verbatim copies
	 of this license document, but changing it is not allowed.

	...........

	详见 [GNU General Public License v3.0](https://choosealicense.com/licenses/gpl-3.0)
	```
- 使用
	- 可以任意使用、拷贝和修改软件。
	- 可以免费分发软件或者出售软件，分发软件的方式不受限制。
	- 不允许修改后和衍生的代码做为闭源的商业软件发布和销售。
	- 只要在一个软件中使用(「使用」指类库引用，修改后的代码或者衍生代码)GPL 协议的产品，则该软件产品**必须也采用 GPL 协议**，既必须也是开源和免费
		- **传染性**
			- 不适用商业软件、对代码有保密要求的人
- 项目
	- Linux
	- Git
	- WordPress
	
## LGPL 
- 历史
	- GNU **宽通用**公共许可证（GNU Lesser General Public License）
		- 由自由软件基金会公布的自由软件授权条款
	- 流行版本
		- V2.1
		- V3	
- 声明
	```
	详见「参考 - 官方」中的 「GNU Lesser General Public License, version 2.1」和「GNU Lesser General Public License」
	```
- 使用
	- 如果对遵循 LGPL 的软件进行任何**改动和/或再次开发并予以发布**，则产品必须继承 LGPL 协议，**不允许封闭源**代码。
	- 但是如果程序只是对遵循 LGPL 的软件进行任何**连接、调用**而不是包含，则**允许封闭**源代码。
- 项目
	- Qt
	
## Apache 
- 历史
	- Apache 许可协议（Apache License）
	- 授予用户适用于**版权**和**专利权**的权利
- 声明
	```
   	详见 [Apache License 2.0](https://choosealicense.com/licenses/apache-2.0)
	```
- 使用
	- 权利
		- 权利的永久性
			- 一旦许可证被授予，就可以无限期使用。
		- 权利的世界性
			- 如果许可证在某一国授予，那么它同时也授予给其它所有国家。
		- 获取权利的免费性
			- 不用预先支付任何使用费，也不用为每次使用或基于其它方式而付费。
		- 权利的非排他性
			- 任何人都能使用经授权的软件。
		- 权利的不可撤回性
			- 权利一旦授予，无人能撤回。
	- 义务
		- 如果修改了代码，需要在**被修改的文件中说明**。
		- 在延伸的代码中（修改和有源代码衍生的代码中）需要**带有原来代码中的协议，商标，专利声明和其他原来作者规定需要包含的说明**。
		- 如果再发布的产品中包含一个**Notice**文件，则在Notice文件中需要带有**Apache Licence**。
			- 你可以在Notice中增加自己的许可，但不可以表现为对Apache Licence构成更改。
- 项目
	- Apache
	- SVN
	- NuGet 
	

# 添加
- 添加新文件，名称为「**LICENSE** | LICENSE.md」
- 选择并填充某种「license」**模板**
- 确认并提交


# 参考
- 作用
	- [如何选择开源许可证？](http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html)
	- [开源许可证教程](http://www.ruanyifeng.com/blog/2017/10/open-source-license-tutorial.html)
	- [如何选择和使用开源许可协议](http://eleveneat.com/2015/12/15/License/)
	- [Various Licenses and Comments about Them](http://www.gnu.org/licenses/license-list.html)
- 明细
	- [Github.Choose license](https://choosealicense.com/)
	- [MIT License](https://choosealicense.com/licenses/mit/)
- 示例
	- [whiteplain/LICENSE](https://github.com/taikii/whiteplain/blob/master/LICENSE)
	- [angular.js/LICENSE](https://github.com/angular/angular.js/blob/master/LICENSE)
- 添加
	- [Licensing a repository](https://help.github.com/articles/licensing-a-repository/)
		- [Applying a license to a repository with an existing license](https://help.github.com/articles/licensing-a-repository/#applying-a-license-to-a-repository-with-an-existing-license)
	- [Adding a license to a repository](https://help.github.com/articles/adding-a-license-to-a-repository/)
- 官方
	- [GNU Operating System](http://www.gnu.org/copyleft/lesser.html)
	- [GNU General Public License, version 2](http://www.gnu.org/licenses/old-licenses/gpl-2.0.html)
	- [http://www.gnu.org/licenses/gpl-3.0.en.html](http://www.gnu.org/licenses/gpl-3.0.en.html)
	- [GNU Lesser General Public License, version 2.1](http://www.gnu.org/licenses/old-licenses/lgpl-2.1.html)
	- [GNU Lesser General Public License](http://www.gnu.org/licenses/lgpl-3.0.html)
	- [Apache License](http://www.apache.org/licenses/LICENSE-2.0.html)
