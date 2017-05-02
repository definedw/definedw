---
title: 微信公众号开发（二）
date: 2017-03-21 12:07:42
tags:
categories: Coding
---

>本文为作者原创，转载请注明出处！

其实说来公众号开发倒不是一定需要配置服务器的**https**，只是上一段时间配置服务器，也可能以后需要在开发小程序的时候可能会用到Https，所以顺带就配置一下吧。

#### SSL

说真的，HTTPS对我来说也是陌生的很，只知道这是一种更加安全的连接方式，老大说HTTPS在信息加密以及在线支付的领域运用得多，但如果是推广的内容的话，就不会使用HTTPS，因为HTTPS不利于优化SEO，具体是如何，还有待研究。个人也是可以创建证书，也可以在第三方平台申请CA证书。但从2016年10月21日开始，Chrome等浏览器不再信任之后的CA证书，所以就算第三方申请得到的免费的证书也可能会被Chrome警告。
<!--more-->
#### 历程

本来是在度娘找的教程申请了一个证书，StartCom，但配置好之后还是被Chrome警告，这里我就不再赘述了，说一下我在其中配置中遇到的问题吧。如果你有一台云服务器的话，我的是Ubuntu：
`openssl req -new -newkey rsa:2048 -nodes -keyout laobuluo.com.key -out laobuluo.com.csr`

当然这个命令是需要你自己变通的，使用该命令之后会生成在你的当前目录下。在这个过程中只需要注意在提示你输入Common Name的时候记得将你的完整域名填进去就可以了。一般Ubuntu都自带了openssl，其余请自行测试，当然其中的解密密码也别忘记了。

#### Apache依赖

开启SSL，Apache需要开启依赖模块，首先可以试试命令`a2enmod ssl`如果系统提示你`Module ssl already enabled`则表示成功，如果没有看到信息，则可以用以下命令：

`ln -s /etc/apache2/mods-available/ssl.conf /etc/apache2/mods-enabled/ssl.conf`
`ln -s /etc/apache2/mods-available/ssl.load /etc/apache2/mods-enabled/ssl.load`

这两条命令是开始SSL模块，网上有些教程说要改动这两个配置文件，其实这和Apache的版本有关系。

关联好模块之后，在你的sites-available下会出现一个default-ssl.conf文件，里面就是默认的SSL配置文件。回到你刚才的生成证书的目录下，将证书文件放到你喜欢的目录下，我放在/etc/apache2/ssl目录下，这样方便以后自己查找等，但请仁者见仁。

编辑你的default-ssl.conf配置，为了方便我还是贴一份我的文件吧：

```
<IfModule mod_ssl.c>
        <VirtualHost _default_:443>
        ServerName www.test.com
        //这里请填写你的证书中的CommonName
        DocumentRoot "/home/test/public/"
                <Directory "/">
                        Options Indexes FollowSymLinks
                        AllowOverride All
                        Require all granted
                </Directory>

        ErrorLog "/var/log/apache2/errorssl-test.log"
        CustomLog "/var/log/apache2/accessssl-test.log" combined
                SSLEngine on
                SSLCertificateFile     /etc/apache2/ssl/1_www.test.com.crt
                //假设我们将生成的证书和Key文件放在/etc/apache2/ssl下，名字分别是
                //1_www.test.com.crt以及2_www.test.com.key
                #SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
                SSLCertificateKeyFile /etc/apache2/ssl/2_www.test.com.key
                #SSLOptions +FakeBasicAuth +ExportCertData +StrictRequire
                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                                SSLOptions +StdEnvVars
                </FilesMatch>
                <Directory /usr/lib/cgi-bin>
                                SSLOptions +StdEnvVars
                </Directory>
                #   "force-response-1.0" for this.
                BrowserMatch "MSIE [2-6]" \
                                nokeepalive ssl-unclean-shutdown \
                                downgrade-1.0 force-response-1.0
                # MSIE 7 and newer should be able to use keepalive
                BrowserMatch "MSIE [17-9]" ssl-unclean-shutdown

        </VirtualHost>
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```

配置多个虚拟主机和80端口是一样的，但注意80端口和443端口不能配置在同一个配置文件里。

配置完成后，由于Ubuntu下Apache的原因，记得关联文件：
`ln -s /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-enabled/default-ssl.conf`

然后重启Apache:`apachectl restart`如遇失败，请检查日志。

**注：**如果在重启遇到需要解密私钥，请进入到私钥目录下，输入如下命令：

`openssl rsa -in test.key -out test.key`

完成后的配置在浏览器里会被警告，没有小绿锁，原因已经说过了，另外介绍大家，腾讯云下赠送免费的证书且有小绿锁。


[测试效果](https://www.goldcao.com)

[To Be Continue!]

