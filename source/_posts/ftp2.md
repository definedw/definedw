---
title: 服务器搭建：（二）SSH登录详解
date: 2016-12-07 16:44:44
tags:
categories: Coding
toc:
---
本文主要讲解SSH登录Ubuntu服务器以及SSH登录Sftp

## SSH生成


使用过Git的人对这个部分很好理解，在Linux和Unix中都可以通过
`ssh-keygen -t rsa -C //生成秘钥 `


* Mac下会保存在系统根目录下的.ssh目录下
* Windows下会保存在你的C盘，用户下.ssh目录下


一般服务器都会叫你新生成秘钥，我所用的腾讯云与AWS是不一样的（后面再讲AWS）


![腾讯云秘钥生成](http://upload-images.jianshu.io/upload_images/2741993-ae4814a011057aac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




在**SSH秘钥**选项下，新建秘钥便会提示你下载，将秘钥下载到本地即可。
为了便于管理，请注意将秘钥文件妥善保存，一般好像过了十分钟之后，便不再提供下载了。当然也有另外一种方式再生成秘钥，这个有空再说。

**注：**新建秘钥后需要与服务器进行关联，否则无法使用。

下载到本地后，如果在Mac下比较方便，Mac登录请参考以下文章


* [MAC下登录服务器踩坑日记](http://www.jianshu.com/p/5f9e62bdc0bd)

此文重点讲Windows下SSH登录。



<!--more-->
<!--toc-->
## SSH登录Ubuntu


因Windows是无法识别秘钥文件的，所以需要以下几个步骤来完成：
参考文献：


* [登录LInux实例](https://www.qcloud.com/document/product/213/5436)

##### 安装Windows远程登录软件


从本地 Windows 机器登录到 Linux 云服务器时，需要使用客户端软件建立连接。这里以使用 PUTTY 为例，参考下载地址：[http://www.putty.nl/download.html。](http://www.putty.nl/download.html%E3%80%82)分别下载putty.exe及puttygen.exe两个文件。

##### 密钥格式转换


打开 puttygen.exe，点击【Load】按钮，在弹窗中首先进入您存放先决条件中下载下来的私钥的路径，然后选择“All File（*.*）”，选择下载好的私钥（例子中为文件david，david是密钥的名称），点击【打开】。


![转换秘钥](http://upload-images.jianshu.io/upload_images/2741993-3e650529a12a2c86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在 key comment 栏中输入密钥名，输入加密私钥的密码（可选），点击【Save private key】，在弹窗中选择您存放密钥的目录，然后在文件名栏输入 密钥名 +".ppk"，点击【保存】按钮。




![秘钥命名](http://upload-images.jianshu.io/upload_images/2741993-acf8d49927019f52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 登录远程Linux云服务器


打开putty.exe，进入【Auth】配置。

![登录Linux](http://upload-images.jianshu.io/upload_images/2741993-191f0e72ae49fb48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


点击【Browse】按钮，打开弹窗后进入密钥存储的路径，并选择密钥，点击【打开】，返回配置界面，进入【Session】配置。

![登录Linux1](http://upload-images.jianshu.io/upload_images/2741993-572a63b1b7f6e9b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在Session配置页中，配置服务器的IP，端口，连接类型。


请注意此部分操作，一定注意安全组port(端口)已经开放，否则无法登录。


##### 其他问题


* 登录遇到 denied key


解决方案：
putty进入命令行模式
`cd G:/git/ //秘钥保存目录`
`chmod 400/600 test.ppk  //秘钥文件 `
就是更改SSH文件的权限，对Linux文件权重不理解的童鞋自行百度。



* 待补充



## SSH登录Sftp


只要拿到了上文里的SSH文件，登录sftp就很简单啦。


上传服务器比较需要注意的是：在腾讯云下，系统默认目录下的/home/ubuntu是不需要系统提供特殊的权限就可以读写的，以Mac下的Transmit为例说明：


![Transmit](http://upload-images.jianshu.io/upload_images/2741993-9e4515db7989f94b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


【To Be Continue!】


