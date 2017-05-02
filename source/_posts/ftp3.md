---
title: 服务器搭建：（三）AWS搭建Shadowsocks
date: 2016-12-07 23:23:09
tags:
categories: Coding
---
本文主要讲解AWS搭建shadowsock VPN

## 为什么选择AWS


作为一名程序员，因为经常需要查找各类文档以及框架的用法，所以很多时候都需要去看看外面的世界，当然你可以购买一些VPN账号来直接访问外网，我自己这么弄只不过是为了折腾而已。

我也是网上找教程的时候，无意中看到了这么个服务器搭建VPS的，因为具有**一年的免费期**当时就想拿来折腾用，至于好坏我们暂且不论。

#### 条件：


* 需要一张支持双币支付的信用卡
* 某宝购买的虚拟信用卡也可以激活，我没试过（道听途说）
* 一个AWS的账号
<!--more-->
<!--toc-->
**注意：**


a. AWS激活的时候，采用的是电话激活（speaking English）,当时懵圈了我三遍才激活，账号注册请自行百度。
b. AWS并不是真正的For Free,这里需要先提醒一下大家，而且AWS扣费的时候，甚至不用通过短信验证，如介意，请出门左转。

## 创建实例：

参考文章：
[科学上网之搭建shadowsock](https://segmentfault.com/a/1190000003101075)

以下命令摘自以上文章：



```
sudo -s //获取root权限
apt-get install update //更新apt
apt-get install python-pip //安装py管理包pip
pip install shadowsocks //安装shadowsock
```


**注意：**
很多人都会在这一步产生错误，如遇错误请先更新你的python


`python update`
 


按照原文教程，直接叫你启动 `ssserver -c /etc/shadowsock.json -d start`这里也会报错，因为安装完shadowsocks后服务器并不会自动生成shadowsocks.json的文件。


正确的做法是你应该先建立：
`vi /etc/shadowsocks.json`

#### 单一端口配置：


```
{
"server":"0.0.0.0", //这里不需要修改为你的服务器IP
    "server_port":   //端口自定义，例如8338
    "local_address":"127.0.0.1",  //默认不用修改
    "local_port":1080, "password":   //密码自己设定
    "timeout":300,  //可以默认
    "method":"aes-256-cfb",  //默认
    "fast_open":false // 默认
}
```

#### 多端口配置


```
{
"server":"0.0.0.0",
"port_password":
         {
        "端口1": "连接密码1",
        "端口2" : "连接密码2"
         },
"timeout":300,
"method":"aes-256-cfb",
"fast_open": false
}
```


配置完成后，别着急，很多时候，你的VPN并不一定可以运行，在文章开始之前，因为我建VPN已经有一段时间了，所以有些东西一下没有总结到位，在你配置完你的AWS后，（我的服务器选择放在了日本Tokyo），在你获得你的公网IP后，应该先到你的控制台ping一下你的IP是否可以 ping通。

![ping失败](http://upload-images.jianshu.io/upload_images/2741993-156044dd14627044.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


无法ping通的Ip是无法用来架设的，你必须更换你的IP，所以你只打算拿AWS做VPN的话，没有必要申请弹性IP，国内过段时间久可能把你可以ping通的Ip封掉，所以你又得换。AWS更换IP的方法就是关闭你的VPS，再重新开机就会给你重新分配IP


完成后，检测一下配置是否成功

`netstat -tunlp`
`ssserver -c /etc/shadowsock.json -d start`

**注：**


有些文章里写道    `ssserver -c /etc/shadowsocks/config.json -d start`


是因为他将配置文件布置在了相应的目录下。

详细参考：
[http://ilanni.blog.51cto.com/526870/1682881](http://ilanni.blog.51cto.com/526870/1682881)


