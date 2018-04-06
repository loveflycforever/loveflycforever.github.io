---
layout: post
title:  "如何安装 Jekyll"
date:   2018-02-01
excerpt: "用于GitHub博客"
tags: [Ruby, Jekyll, 安装]
feature: https://jekyllrb.com/img/logo-2x.png
comments: true
---
### 下载 Ruby

> https://github.com/oneclick/rubyinstaller2/releases/download/rubyinstaller-2.4.3-2/rubyinstaller-2.4.3-2-x64.exe

- 运行安装

- 查看 ruby 版本

`ruby -v`

### 下载 RubyGems

> https://rubygems.org/rubygems/rubygems-2.7.6.zip

- 解压至某目录下，运行命令行安装

`ruby setup.rb`

- 查看 gems 版本

`gem -v`

### 下载 MSYS2

MSYS2是一个用于Windows的软件发行版和构建平台。

> http://repo.msys2.org/distrib/x86_64/msys2-x86_64-20161025.exe

- 运行安装

- 运行命令行为 ruby 安装

`ridk install`

### 安装 Jekyll

`gem install jekyll`

- 查看 jekyll 版本
`jekyll -v`

### 运行 Jekyll

`jekyll server`

可能模板目录中的Gemfile文件Jekyll版本信息不符，注意修改。

---
---
### 【Extra 001】给 RubyGems 添加国内的源

执行`gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/`命令

然后使用`gem sources -l`命令，确保只有`gems.ruby-china.org`存在。

### 【Extra 002】给 MSYS2 添加国内的源

修改 msys64\etc\pacman.d 目录下三个文件。 
#### mirrorlist.msys 
```
##
## MSYS2 repository mirrorlist
##

## Primary
## msys2.org
Server = http://mirrors.ustc.edu.cn/msys2/msys/$arch/
Server = http://repo.msys2.org/msys/$arch
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MSYS2/$arch
Server = http://www2.futureware.at/~nickoe/msys2-mirror/msys/$arch/
```

#### mirrorlist.mingw64 
```
##
## 64-bit Mingw-w64 repository mirrorlist
##

## Primary
## msys2.org
Server = http://mirrors.ustc.edu.cn/msys2/mingw/x86_64/
Server = http://repo.msys2.org/mingw/x86_64
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MINGW/x86_64
Server = http://www2.futureware.at/~nickoe/msys2-mirror/x86_64/
```

#### mirrorlist.mingw32
```
##
## 32-bit Mingw-w64 repository mirrorlist
##

## Primary
## msys2.org
Server = http://mirrors.ustc.edu.cn/msys2/mingw/i686/
Server = http://repo.msys2.org/mingw/i686
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MINGW/i686
Server = http://www2.futureware.at/~nickoe/msys2-mirror/i686/
```

### 【Extra 003】

> RubyInstaller，在Windows上安装Ruby的简单方法
> - https://rubyinstaller.org/

> Ruby 社区的 Gem 托管服务
> - https://rubygems.org/

> MSYS2 installer，一个用于Windows的软件发行和搭建平台
> - http://www.msys2.org/

> RubyGems 镜像
> - https://gems.ruby-china.org/

> Jekyll，一个简单的免费的Blog生成工具
> - https://jekyllrb.com/

> Jekyll 模板
> - http://jekyllthemes.org/
