---
title: 关于Mac下配置Apache
date: 2017-03-09 10:32:59
tags:
categories: Coding
---

>本文为作者原创，转载请注明出处！


个人因为正在前端路上，加上原因换了新mac，需要再另行配置电脑服务器环境，记录下其中所遇到问题，留待查阅。
**注:OS Captian EI 11.4**

#### Apache2

基本mac都会自带Apache、Php等，具体查看命令 `apachectl -v`

![Version](http://upload-images.jianshu.io/upload_images/2741993-0383ef72d51e694f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不同版本之间会有些许区别。
<!--more-->

Mac下的Apache配置文件保存在** /etc/apache2/ **目录下，目录如下
![Mulu](http://upload-images.jianshu.io/upload_images/2741993-e8cb137fdf663fb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


建议操作之前先做备份，这样有利于应对严重后果。操作apache目录一般都需要root权限，退回** /etc/ **下
`sudo cp -R apache2 /etc/apache2_backup`

#### 具体操作

unix下的基本vim请自行百度，如果你的apache只需要配置单个端口的话，只需要在** httpd.conf **文件中修改配置即可：
* 找到LoadModule alias_module libexec/apache2/mod_alias.so将前面的** # **去掉
* 去掉LoadModule rewrite_module libexec/apache2/mod_rewrite.so的** # **
* 找到 


``` 


<Directory />
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```


将配置修改如上。保存退出** apachectl restart **重启Apache，在浏览器里访问127.0.0.1,出现** is work **，则基本差不多了。



#### 配置虚拟主机（VirtualHost）



1. 配置虚拟主机更符合我们的开发习惯，首先在httpd.conf配置中放开Include /private/etc/apache2/extra/httpd-vhosts.conf注释。
2. 进入目录** ~/apache2/extra/ **,编辑配置httpd-vhosts.conf,系统默认有两个虚拟主机，只不过基本没什么作用：



``` 
<VirtualHost *:80>
    ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/usr/docs/dummy-host.example.com"
    ServerName dummy-host.example.com
    ServerAlias www.dummy-host.example.com
    ErrorLog "/private/var/log/apache2/dummy-host.example.com-error_log"
    CustomLog "/private/var/log/apache2/dummy-host.example.com-access_log" common
</VirtualHost>


```



**ServerAdmin:**网站管理者，一般填写邮件地址，个人开发无所谓。
**DocumentRoot:**你设置的个人网站根目录，在设置这个目录的时候，很多时候都会受到权限的影响而报错而产生permission denied,请大家注意。
**ServerName:**这就是你想要设置的访问名字了，由你自己定

放一份我个人的配置


``` 
<VirtualHost *:80>
    ServerAdmin sanchuan@linofu.dev
    DocumentRoot "/Users/sanchuan/Documents/workspace/html5"
    ServerName linofu.dev
    #ServerAlias www.dummy-host.example.com
    ErrorLog "/private/var/log/apache2/linofu.dev-error_log"
    //另外对新手说一下，请最好记住你的错误日志，因为配置的时候，可以
    //tail /private/var/log/apache2/linofu.dev-error_log就可以在控制台中找到问题，再去找别人的时候更好解决。
    CustomLog "/private/var/log/apache2/linofu.dev-access_log" common
    <Directory "/">
        Options Indexes  FollowSymLinks
        //这句在这里将可以配置你的服务器在没有Index的时候，产生目录结构。见下图
        #AllowOverride All
        #Order deny,allow
        #Allow from all
        Require all granted
    </Directory>
</VirtualHost>


```


![Index](http://upload-images.jianshu.io/upload_images/2741993-c71e0e55bf47e64d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



3. 保存配置退出，回到 **/etc/**下，编辑你的**hosts**文件；将你的**ServerName**绑定在127.0.0.1后。重启apache

PS：如果你觉得配置实在麻烦的话，Mac下有集成环境，具体用什么，你可以自己决定。


[To Be Continue]

<!--toc-->

