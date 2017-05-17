---
title: Ubuntu服务器下（TXY）建立Nginx以及配置Nodejs
date: 2016-12-07 10:47:13
tags:
categories: Coding
---
最近对服务器折腾比较多，中间确实是踩了很多的坑。
记录下来，方便以后自己查阅：

#### 搭建Nginx ####


Nginx是什么，这里我就不做多的解释了，购买服务器之后，基本都要配置服务器环境，Nginx就是服务器的一个配置环境，用来跑你存在服务器里的Index,php,htm等文件。



1. 如何搭建Nginx：


**我这里只讨论TXY Ubuntu系统下的部署**
在官方的文档里，>最佳实践下描述了Centos,Ubuntu的命令其实差不多：
```apache
sudo -s //获取root权限
apt-get install nginx
service nginx start //启动nginx
```


2. 配置Nginx：配置Nginx多个端点
 很多小伙伴估计都是对多个端点配置感兴趣，所以这里就不对单一的Nginx配置讲解了。单一的配置主要就是对系统里存在的Nginx目录下的一些文件路径的替换。
开始之前，简单的讲解下linux下的**vi**
```
vi /root/etc  //文件路径名字
i  INSERT 输入 
按下ESC退出编辑状态
Shift+: = :   >q退出 q!强制退出 wq 保存退出  
非编辑状态下 ?****向上查找 /****向下查找
```
<!--more-->

<!--toc-->


我采用的是配置Nginx虚拟服务：
a 进入你的Nginx安装目录下，TXY在
`cd /etc/nginx`
b 创建新的文件，配置虚拟接口
`mkdir vhosts //名字可以自己设定`
c 进入到vhosts文件夹下：创建你的端口文件,这里网上教程很多，我的方式只是和网上一样，只具有参考性。
`vi site1.com.conf //创建第一个端口配置`

```nginx


server {
        listen  80;
        server_name  ****.com www.****.com;
        access_log  /var/log/nginx/access.log;
        location / {
            root  /home/ubuntu/*****;//自己文件的路径
            index  index.php index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root  ~/;
        }
       # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        location ~ .php$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /home/site1.com/$fastcgi_script_name;
            include        fastcgi_params;
        }
        location ~ /.ht {
            deny  all;
        }
}
```


然后再重复C步骤，即可创建多余的端口了，大家在这里配置的时候，需要多用心，我在这里错了很多次，因为access_log这个路径也会因为错误而报错，这里我具体没有什么太好的解决方案。
d 配置完成后，我们需要配置nginx的主配置文件
` /nginx/nginx.conf`
需要将我们刚才配置好的 `site*.com.conf `的多个文件添加在http{}里面：
这里大家请注意，网上有些教程，说的是叫直接添加在 `nginx.conf `文件里，但我第一次这么配置的时候，Nginx -t 失败了，但后来我没去验证这个问题，我只是将我的` include /etc/nginx/vhosts/*.conf`加载在http块下就成功了。

**注意：**


配置完成后请一定要检查配置文件：`nginx -t`
`nginx -t -c /root/etc/nginx/vhost.*/ //这里是你自己配置好的conf路径`
`nginx -t -c /root/etc/nginx/nginx.conf/`

如果检查没有提示Error灯字样，那么恭喜你，成功了一半了。
还有比较坑的一步：Ubuntu下重启Nginx命令与Linux是不一样的，大家一定要注意
 <b>service nginx reload/restart/****</b>

至此大家就可以开心的往你的服务器里传index.html,向别人展示你的网页啦~



#### 配置Nodejs ####


Node似乎对linux 不是很友好，刚开始的时候我是用的

`apt-get install npm    //`
`apt-get install node //`


但是这两个命令虽然成功了，但当你在命令行里输入：`node -v`
那Ubuntu就会报错，No Command 或者别的，网上有关于Ubuntu下应该安装nodejs >>`apt-get install nodejs`

确实这样安装之后你可以使用`nodejs -v`注意中间没有  ·，查询到node的版本，但对全局里的node命令依然无效，我对node的了解不是很多，所以这给出的只是我自己的方式：

参考文章：


* [Linux下Nodejs安装](https://my.oschina.net/blogshi/blog/260953)


建议大家不要选用第二种，我自己试了一下，比较麻烦。



1. 配置node，我在Node官网下载了两个版本，第一个Node-v4.4.4还有一个node-v6.2.1，都是稳定版的，然后用一个sftp上传到了服务器，
`tar zxvf node-v**********.gz //Linux解压`
进入到解压目录内，我的目录结构如下：

![Node目录](http://upload-images.jianshu.io/upload_images/2741993-05457aa0c638d77c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


按照上文的方法我试了`ln -s  /home/ubuntu/node-v4.4.4/bin/node /usr/local/bin/node`
以及`ln -s /home/ubuntu/node-v4.4.4/bin/npm /usr/loacal/bin/npm`

**注意：** 


/home/ubuntu/node-v4.4.4/bin/node 
是你的Node的目录结构 
/usr/loacal/bin/node 是全局
此种方法后，然后全局`node -v`成功，但是`npm -v`报错merge,找了许久没有找到方法解决，我不知道这是不是TXY独有的情况，我后来只好重装了系统。


2. 反复试了很多次，最后我成功的方案如下：
a `apt-get install npm`先在系统下内置安装npm
b `apt-get install nodejs` 再安装nodejs
c 然后再按第1步骤来>>`tar zxvf node-v****`
`ln -s /home******`
`ln -s /h ome*****`

然后全局`node -v`成功
`npm -v`成功，以上过程如遇Premission denied 记得添加`sudo`

至此，服务器node配置完毕！



>To Be Continue!


