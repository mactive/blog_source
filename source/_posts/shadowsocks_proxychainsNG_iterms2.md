title: ShadowSocks+ProxyChains-NG 实现终端(iTerm2)下代理
date: 2016-04-19 08:31:15
author: [mac.meng]
tags: [ShadowSocks,ProxyChains-NG,iTerm2]
---
[参考原文](https://segmentfault.com/a/1190000004607285)

### 安装
使用 Homebrew 安装 proxychains-ng
```shell
  ~ $ brew install proxychains-ng
```
### 编辑配置文件 

```shell
  vim /usr/local/etc/proxychains.conf
```

在 [ProxyList] 下面（也就是末尾）加入代理类型，代理地址和端口
注释掉原来的代理并添加, 1080 就是默认的shadowSocks 本地代理端口号,

```shell
  socks5 127.0.0.1 1080
```

如果有的软件支持socks5代理, 比如 **dropbox** ,可以直接设置, 
> 我们在这里搞这么多飞机就是因为, iTerm2 不支持配置代理,而且就算你在系统设置了也不管用.

如果所在的网络很复杂，可能需要在配置文件中启用
dynamic_chain - 按照列表中出现的代理服务器的先后顺序组成一条链，如果有代理服务器失效，则自动将其排除，但至少要有一个是有效的
然后在 [ProxyList] 下添加多个代理
默认：strict_chain - 按照后面列表中出现的代理服务器的先后顺序组成一条链，要求所有的代理服务器都是有效的

### 测试
  proxychains4 curl google.com
注意：proxychains支持的是socks，http, https协议.它们以tcp或者udp协议为基础, ping命令用的是 ICMP 协议， proxychains 不支持;

### 优化
alias 命令别名
直接在命令行中输入

```shell
  ~ $ alias pc="proxychains4”
```

iTerm中前缀补全
你输了很长一段命令，然后你突然想使用代理功能，怎么办？
我想你应该会复制一下，然后重写；
现在你可以这样做：

在 iTerm -> Preferences -> Profiles -> Keys 中，新建一个快捷键，例如 ⌥(alt) + p ，Action 选择 Send Hex Code，键值为 0x1 0x70 0x63 0x20 0xd，保存生效。这个 HexCode 用 [hexCodeToAsciiText](http://www.rapidtables.com/convert/number/hex-to-ascii.htm) 就是 “pc”两个字母

以后命令要代理就直接敲命令，然后 ⌥ + p 即可，这样命令补全也能保留了。
比如 git clone, npm install 加速等等.

### 推荐(福利)
最后推荐一个下载视频的神器 [you-get](https://github.com/soimort/you-get)可以下载国内外主流的视频网站的视频,made by python3. 也可以配置 proxychains4使用

```shell
  ~ $ pc you-get https://www.youtube.com/watch?v=fwoDwajwqdo
```

### 谜团
关于使用shadowSocks 时,dns是不是一起走了代理了,这里面还有很多网络知识不是特别明白
