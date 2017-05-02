---
title: 微信公众号开发（一）
date: 2017-03-15 10:20:38
tags:
categories: Coding
toc: true
---

#### 基本流程

说实话官方文档真是让人很纠结，看了会，还是有很多地方没看明白，各种查找各种坑！

* **系统：**Mac Os EI captian11.4
* **服务器：**Ubuntu14.04
* **环境：**PHP5+MYSQL+APACHE2.4.7
* **软件：**transmit


具体公众号的申请什么就不再赘述，请自行百度。[官方Wiki](https://mp.weixin.qq.com/wiki)。

公众号申请之后，进入开发者权限，先基本配置连通自己服务器，百度上有些教程采用的是SAE什么的我没有试，但想来应该大同小异。进到公众号平台后进入基本配置
<!--more-->

![公众号](http://upload-images.jianshu.io/upload_images/2741993-832a28c5835302f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 服务器配置

服务器环境在Linux中可以直接配置LAMP集成环境，只是我没有采用这种方法，优劣不知。请自我尝试。

1. 配置Apache`sudo apt-get install apache2`
2. 配置Mysql`sudo apt-get install mysql-sql`，安装时会叫你设置root密码，同时配置一下依赖`apt-get install libapache2-mod-auth-mysql`，为支持php再安装一下`apt-get install php5-mysql`。
3. 配置Php`sudo apt-get install php5`，`apt-get install libapache2-mod-php5`

 

#### Apache虚拟主机

在Ubuntu下，Apache文件在**/etc/apache2/**目录下，系统默认目录在**/var/www/**，如不需要虚拟主机也可以直接在index等文件放在默认目录下。

**注：**Linux下Apache的主配置文件为apache2.conf，并不是网上大部分说的htttpd.conf，但基本配置都差不多，只是Linux下的Apache分得更加细，我想这也许是为了更加方便管理吧。默认的文件不需要改动，如果你需要自己设置配置在httpd文件夹内，请将以下语句加进主配置文件内`IncludeOptional httpd/*.conf`

![apache](http://upload-images.jianshu.io/upload_images/2741993-3dc0c0d9e5bc9775.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

以上为目录结构。

进到sites-avaiable编辑000-default.conf文件以配置虚拟主机，注意其实在sites-avaiable文件内配置的项目并没有直接生效，而需要在sites-enable里将配置文件关联起来，这样的深意在哪里，我就不得而知了。
假如我们在sites-avaible目录下新建`vi test.conf`

```
<VirtualHost *:80>

        ServerName  dev.test.com
        //填写你自己的域名
        DocumentRoot "/home/test/weixin/public/"
        //自定义目录，请将目录权限开到755以上，避免premission denied
                <Directory "/">
                        Options Indexes FollowSymLinks
                        AllowOverride All
                        Require all granted
                </Directory>

        ErrorLog "/var/log/apache2/errordev-test.log"
        CustomLog "/var/log/apache2/accessdev-test.log" combined

</VirtualHost>
```


关联命令`sudo ln -s /etc/apache2/sites-available/test.conf /etc/apache2/sites-enabled/test.conf`

一般来说我们都是修改默认配置文件000-default.conf，具体如何配置，请根据自己的情况选择。

啰嗦一句，这里首先最好先测试一下PHP，在很多情况下，PHP总会出一些莫名其妙的问题，可以在你的DocumentRoot目录下`vi index.php`，将以下内容放进去`<?php phpinfo(); ?>`，然后浏览器访问，出现如下界面则保证PHP正常运行：

![php](http://upload-images.jianshu.io/upload_images/2741993-0112f0f861cf1319.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如没有正常显示，请百度自行解决。


#### 配置微信接口

绕了这么大一个圈，终于来到我们的目的，首先在Wiki里下一份测试PHP配置，地址：[PHP示例代码](http://mp.weixin.qq.com/mpres/htmledition/res/wx_sample.20140819.zip)

我通过transmit用ftp将本地文件传到服务器上，具体你采用何种方式，请自我斟酌。

![Setting](http://upload-images.jianshu.io/upload_images/2741993-cafe5c697f62be6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后提交你的配置，成功的话，就恭喜你已经将基本配置完成好了。

[To Be Continue!]



