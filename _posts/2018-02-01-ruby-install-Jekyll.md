---
layout: post
title:  "如何在 Windows 环境下安装并运行 Jekyll 服务"
subtitle: "基于 GitHub 博客的部署的实践记录"
tags: [GitHub, Ruby, Jekyll, 安装]
bigimg: https://jekyllrb.com/img/logo-2x.png
comments: true
---
### 下载 Ruby

Ruby 是一种面向对象、命令式、函数式、动态的通用编程语言。 

> https://github.com/oneclick/rubyinstaller2/releases/download/rubyinstaller-2.4.3-2/rubyinstaller-2.4.3-2-x64.exe

- 下载并运行

- 完成后，查看 Ruby 版本

`ruby -v`

### 下载 RubyGems

RubyGems 是 Ruby 的一个包管理器，提供了分发 Ruby 程序和库的标准格式`gem`，旨在方便地管理`gem`安装的工具，以及用于分发`gem`的服务器。

> https://rubygems.org/rubygems/rubygems-2.7.6.zip

- 解压并运行安装目录中的`setup.rb`文件

- 完成后，查看 RubyGems 版本

`gem -v`

### 下载 MSYS2

MSYS2 是一个用于 Windows 环境下的软件发行版和构建平台。

> http://repo.msys2.org/distrib/x86_64/msys2-x86_64-20161025.exe

- 下载并运行

- 在命令行窗口中运行以下命令

`ridk install`

** 注意 ** 在 Ruby 安装结束后会有勾选安装 MSYS2 的选项，可以默认选择执行。

- 出现以下界面，输入数字 <kbd>3</kbd>，然后按下回车键 <kbd>Enter</kbd>

```
 _____       _           _____           _        _ _         ___
|  __ \     | |         |_   _|         | |      | | |       |__ \
| |__) |   _| |__  _   _  | |  _ __  ___| |_ __ _| | | ___ _ __ ) |
|  _  / | | | '_ \| | | | | | | '_ \/ __| __/ _` | | |/ _ \ '__/ /
| | \ \ |_| | |_) | |_| |_| |_| | | \__ \ || (_| | | |  __/ | / /_
|_|  \_\__,_|_.__/ \__, |_____|_| |_|___/\__\__,_|_|_|\___|_||____|
                    __/ |           _
                   |___/          _|_ _  __   | | o __  _| _     _
                                   | (_) |    |^| | | |(_|(_)\^/_>

   1 - MSYS2 base installation
   2 - MSYS2 system update
   3 - MSYS2 and MINGW development toolchain

Which components shall be installed? If unsure press ENTER [1,2,3]
```

### 安装 Jekyll

Jekyll is a simple, blog-aware, static site generator for personal, project, or organization sites. 

- 在命令行窗口中运行以下命令

`gem install jekyll`

- 完成后，查看 Jekyll 版本

`jekyll -v`

- 运行 Jekyll

`jekyll server`

** 注意 ** 可能模板目录中的 Gemfile 文件中指定 Jekyll 版本信息不符，如有需要可以修改。

### 意料外的问题

- 类型一，缺少指定包文件

```
/Ruby24-x64/lib/ruby/site_ruby/2.4.0/bundler/resolver.rb:289:in `block in verify_gemfile_dependencies_are_found!': Could not find gem 'github-pages (= 197) x64-mingw32' in any of the gem sources listed in your Gemfile. (Bundler::GemNotFound)
```

运行以下类似命令，参数`-v`用于指定版本号

`gem install github-pages -v 197`

- 类型二，约束命令

```
/Ruby24-x64/lib/ruby/site_ruby/2.4.0/bundler/runtime.rb:313:in `check_for_activated_spec!': You have already activated liquid 4.0.3, but your Gemfile requires liquid 4.0.0. Prepending `bundle exec` to your command may solve this. (Gem::LoadError)
```

在需要执行的命令前添加`bundle exec`，譬如，`bundle exec jekyll server`

- 类型三，时区问题

```
Error:  No source of timezone data could be found.
Please refer to http://tzinfo.github.io/datasourcenotfound for help resolving this error.
```

需要在 Gemfile 文件中添加

```
gem 'tzinfo-data'
```

并且这个安装包文件

`gem install tzinfo-data`

---

---

### 【Extra 001】给 RubyGems 添加国内的源
执行`gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/`命令

然后使用`gem sources -l`命令，确保只有`gems.ruby-china.com`存在。

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
