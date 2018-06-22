---
title: mac开发手册配置
date: 2018-06-03 09:13:44
tags:
---

#### 安装Xcode command line tools
```
$xcode-select --install
```
安装目录：/Library/Developer/CommandLineTools/

#### Homebrew安装
> Homebrew是mac下的包管理工具
```
# 1. 安装Homebrew
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# 2. 设置环境变量
$ echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bash_profile
$brew doctor

# 3. 安装Homebrew cask
homebrew cask是mac下编译好的的软件包管理
```

#### 命令行工具利器iTerm2
配置好用的命令行工具

#### sublime 编辑器安装
```
# 1.设置命令行打开
$ ln -s /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl /usr/local/bin/subl

# 2. Package Control安装
#command + ` 输入一下内容
import urllib.request,os,hashlib; h = ‘7183a2d3e96f11eeadd761d777e62404’ + ‘e330c659d4bb41d3bdf022e94cab3cd0’; pf = ‘Package Control.sublime-package’; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( ‘http://sublime.wbond.net/‘ + pf.replace(‘ ‘, ‘%20’)).read(); dh = hashlib.sha256(by).hexdigest(); print(‘Error validating download (got %s instead of %s), please try manual install’ % (dh, h)) if dh != h else open(os.path.join( ipp, pf), ‘wb’ ).write(by)

# 3. cmd + shift + p 进行插件管理
```


#### 效率工具
- 1Password 密码管理工具
- Alfred 2 搜索工具
- Spectacle 窗口等比例显示
- Lantern 科学上网
- CheatSheet 快捷键


