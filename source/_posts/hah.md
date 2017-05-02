---
title: 你不知道的HEXO deploy
date: 2016-12-26 15:31:23
tags:
categories: Coding
---

> 本博文为本人原创，转载需注明出处：

因hexo搭建也有一段时间了，刚开始部署的时候，直接部署在了服务器上，还是有些不方便，每次写完都要vi。后来查查文档，发现了几种部署到服务器端的方法，自己特意找了一种试试，记录一下，方便自己，尽量方便大家。
 
 
 **注：**


本教程指next主题部署文件至服务器，不是Git。
 
官方提供了几种部署方案，我这里选择了ftpsync,至于为什么，只是因为我在用rsync遇到了一个比较严重的问题没有解决。等我研究明白了，再来补充。


 
 <!--more-->
 <!--toc-->
 
 ![ftpsync](http://upload-images.jianshu.io/upload_images/2741993-7338e2cfc5f7bddf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
 
以上是文档里提到的ftpsync的配置项，我看到有Blog上说这个插件会将本地目录里的所有文件同步成服务器一样的，但经过我测试，上传的时候只会上传public文件夹。所以ingore配置项你可以默认，由于我是将文件上传到服务器，为了方便所以我专门在我的服务器上创建了新用户专用作ftp上传：


 `adduser ***//自己设置名称`
 
 然后会提示你设置密码，然后一直默认设置就行了。这个命令比


 `useradd ***`
 `useradd *** pass`


方便多了

我服务器是ubuntu,其余操作系统请自行搜索。

# error1

将配置文件_confiig.yml配置完成后，然后想着开心的

`hexo g -d`

然后报错 530 incorrect login

无论是在ftp里登录还是别的地方都报这个错误，没办好，只好先找度娘，最后不知道在哪位大哥的博客下找到了解决方案，只不过写的时候已经找不到那个链接了。报这个错误的主要原因是因为服务器端的vsftpd文件报错了：

**解决方法**


```
$ sudo -s
$ vi /etc/vsftpd.conf
$ //找到 #write_enable=YES 将前面的#去掉
```

# error2

登录的问题解决了，其实心里还在打鼓，果不其然：

```
$ hexo clean
$ hexo g -d
```
还是报错，当时的错误代码没有截图，以致于现在不能给大家上原图了。但大致源码我还是可以写下来，大家自己对照吧。


```
Colletting:
Errors events.js:160
        throw: ***
        
        TCP: ***
        onread: ***
```
 出现这个问题，建议大家还是找一下node的问题吧，我找了许久的答案最后也没能够说个所以然来,我当时的Node6.9.1。后来我是将自己的node重装了一遍，而且装的是Node4.7.0版本。这个问题就自动解决好了，如果有人知道，希望能不吝赐教。
 

# error3

经历了前两个错误，我知道第三个错误也是避免不了的。再次部署`hexo g -d`遇到:
**premission denied 550**

一般操作过Linux系统的人都会知道这是因为缺少了权限，所以我先将服务器上的接收目录的权限放到了最大
`$ chmod -R 777 ***//目录名`
可依然还是报错550 


```
MKDIR fail
      ***
550 : Cannot create a file when that file already exists
```

找来找去最后找到了这个大哥的Blog下：从他的Git下拉了一个src/parse.js文件修复了这问题。
 
[踩坑道人Blog](http://k99k.com/2015/hexo-ftpsync-bug/) 


然后一路开心的`hexo g -d`
没报错，然后打开自己的的博客，哈哈显示正常，然后再点文章：


然后



# error4


nginx 403 Forbidden
心里当真是问候了很多句苍天。没办法，继续折腾呗。我没有上面这位大哥厉害，console了三个小时，耐心地找了会百度就找到了一个解决方案：


```
$ vi /etc/nginx.conf
//在第一行的位置有个user 将其修改为你的ftp账号如下：
//修改 ftp账号
$ user ftpuser
$ worker_processes
//重启Nginx:
$ service nginx restart
```


问题大概就这么多，欢迎交流指正。


 
#### 参考资料：
[hexo文档](https://hexo.io/zh-cn/docs/index.html)

