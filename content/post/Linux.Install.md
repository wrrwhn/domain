---
title: "Linux.Install"
date: "2018-08-08"
categories:
 - "整理"
tags:
 - "Linux"
toc: true
---


# 方式
## RPM
### 描述
- 全称为 `Redhat Package Manager`，支持安装、卸载、升级、查询和验证

### 指令
- cmd
    - `rmp [option] <soft.rpm|soft>`
- option
    - `-a`
        - 查询所有套件
    - `-b<stage><soft>+或-t <stage><soft>+`
        - 设置包装套件的完成阶段，并指定套件档的文件名称
    - `-e<soft>或--erase<soft>`
        - **删除**指定的套件
    - `-f<file>+`
        - 查询拥有指定文件的套件
    - `-h或--hash`
        - 套件安装时列出标记
    - `-i`
        - 显示套件的相关信息
    - `-i<soft>或--install<soft>`
        - **安装**指定的套件档
    - `-l`
        - 显示套件的文件列表
        - `-c`
            - 只列出组态配置文件，本参数需配合"-l"参数使用
        - `-d`
            - 只列出文本文件，本参数需配合"-l"参数使用
        - `-s`
            - 显示文件状态，本参数需配合"-l"参数使用
    - `-p<soft>+`
        - 查询指定的RPM套件档
    - `-q`
        - 使用 **询问模式**，当遇到任何问题时，rpm指令会先询问用户
    - `-R`
        - 显示套件的关联性信息
    - `-U<soft>或--upgrade<soft>`
        - **升级**指定的套件档
    - `-v`
        - **显示**指令 **执行过程**
    - `-vv`
        - **详细显示**指令执行过程，便于排错

### 示例
- 安装 `rpm` 包
    - `rpm -ivh soft.rmp`
    - `rpm --force --nodeps -ivh soft.rmp`
        - 覆盖安装
        - 忽略未安装此包需要的一些软件

- 安装 `.src.rpm` 包
    - 该软件包含源代码，需在安装时进行编译

    ```sh
    # 方法一
    rpm -i soft.src.rpm
    cd /usr/src/redhat/SPECS && rembuild -bp soft.specs
    cd /usr/src/redhat/BUILD/soft/ && ./configure && make && make install

    # 方法二
    rpm -i soft.src.rpm
    cd /usr/src/redhat/SPECS && rembuild -bp soft.specs
    cd /usr/src/redhat/RPM/(i386|i686|noarch) && rpm -8 <new-soft>.rpm
    ```

- 卸载 `rpm` 包
    - `rpm -e <soft[-version]>`
    - `rpm -e --nodeps <soft[-version]>`
        - 该软件为其它软件所须，但仍强制删除

- 罗列系统中安装过的 `rpm` 包
    - `rpm -qa`

- 查看指定软件的安装目录
    - `rpm -ql <soft>`


## tar.gz | tar.bz2
### 描述
- 源代码方式安装
    - 默认安装目录为 `/usr/local/bin`
        - 但具体参见该软件的 `INSTALL` 和 `README`

### 示例

```sh
# tar.gz
wget http://downloads.xiph.org/releases/ogg/libogg-1.3.3.tar.gz
tar **xzvf** libogg-1.3.3.tar.gz
cd libogg-1.3.3
## 配置，根据需求调整生成目录
./configure --prefix="/usr/local/ffmpeg" --disable-static
## 编译
make   
## 安装                                     
make install
## 移除安装时产生的是临时文件
make clean

# tar.bz2
tar **-jxvf** libogg.tar.bz2
```

## YUM
### 描述
用于解决普通 `RPM` 包安装时关联性太大的问题
自动下载并安装 `RPM` 包

### 指令
- cmd
    - `yum [option] [args] [<soft>]`

- option
    - `-h`
        - 显示帮助信息
    - `-y`
        - 对所有的提问都回答 “**yes**”
    - `-c`
        - 指定配置文件
    - `-q`
        - 安静模式
    - `-v`
        - 详细模式
    - `-d`
        - 设置调试等级（0-10）
    - `-e`
        - 设置错误等级（0-10）
    - `-R`
        - 设置yum处理一个命令的**最大等待时间**
    - `-C`
        - 完全从缓存中运行，而不去下载或者更新任何头文件

- args
    - `install`
        - **安装**rpm软件包
    - `update`
        - **更新**rpm软件包
    - `check-update`
        - 检查是否有可用的更新rpm软件包
    - `remove`
        - **删除**指定的rpm软件包
    - `list`
        - 显示软件包的信息
    - `search`
        - 搜索软件包的信息
    - `info`
        - 显示指定的rpm软件包的描述信息和概要信息
    - `clean`
        - 清理yum过期的缓存
    - `shell`
        - 进入yum的shell提示符
    - `resolvedep`
        - 显示rpm软件包的依赖关系
    - `localinstall`
        - **安装本地**的rpm软件包
    - `localupdate`
        - 显示本地rpm软件包进行更新
    - `deplist`
        - 显示rpm软件包的所有依赖关系

### 示例
- 安装

    ```sh
    yum install
    yum install <soft>
    yum groupinstall <group>
    ```
- 更新

    ```sh
    yum update
    yum update <soft>
    yum check-update
    yum groupupdate <group>
    ```

- 查看

    ```sh
    yum info <soft>
    yum list
    yum list <soft>
    # 查看软件的依赖情况
    yum deplist <soft>      
    yum groupinfo <group>
    ```

- 删除

    ```sh
    yum remove <soft>
    yum groupremove <group>
    ```

- 清除缓存

    ```sh
    yum clean <soft>
    # 清除已下载的 rpm 包
    yum clean packages
    # 清理 /var/cache/yum 中的 headers 信息
    yum clean headers
    yum clean oldheaders
    ```

# 参考
- [Linux安装软件总结（二.几种安装命令介绍）](http://zyjustin9.iteye.com/blog/2026579)
- [rpm命令](http://man.linuxde.net/rpm)
- [yum命令](http://man.linuxde.net/yum)
- [Centos Ram缓存清理](https://www.jianshu.com/p/ddbe5622d5e4)