---
title: ubuntu服务器搭建SVN记录
date: 2017-05-09 15:07:35
tags:
categories: Coding
---

>本文为作者原创，转载请注明出处！

svn，是一个开放源代码的版本控制系统，与git差不多的功能。

### 构建服务器

说实话，这东西还是需要自己去折腾。
虽然网上的教程很多，但还是推荐看文档里的，很多人写到的不是少了这里就是那里，这种碎片化的知识，真的解决起来特别麻烦。

**推荐官方文档：**

>subversion: http://wiki.ubuntu.org.cn/SubVersion
>trac: http://wiki.ubuntu.org.cn/Trac%E7%9A%84%E5%AE%89%E8%A3%85%E8%AE%BE%E7%BD%AE

**主要注意点：**

1. 创建文件仓库的时候注意权限，由以上文档中提到的，在创建仓库时候，先需要添加组。在Linux下查看默认的用户组：groupmod+tab键则可以显示所有的组；也可以vim /etc/group查看组。

2. 如果你想创建一个新的用户来专门管理你的SVN仓库：假如你创建用户svn，请注意将你的svn添加到subversion组。`sudo adduser svn subversion`。

3. 另外一个就是权限问题，于仓库目录（假设仓库为repos)创建时如果在root用户下，`chown -R svn:subversion /home/svn/repos `。我把自己的目录改到了svn 用户下，然后再将权限改为可以读写的g+rws。具体参照wiki。

### Apache2 Subversion

利用Apache服务器来访问远程仓库，需要开启Apache SVNmod：


* `sudo apt-get install libapache2-svn`，其余需要请查阅wiki。
* `sudo a2enmod dav_svn`

开启模块之后，则对以下文件做修改。贴一份配置：


```apache
<Location /svn>  #/svn表示http://hostname/svn/myproject
  DAV svn
  SVNParentPath /home/svn #配置仓库父目录
  AuthType Basic
  AuthName "SC Project Svn"
  AuthUserFile "/etc/subversion/passwd"  #svn用户文件
  AuthzSVNAccessFile "/etc/subversion/authz" #授权访问文件
  Require valid-user
</Location>
```

如需要配置虚拟主机：


* `cp 000-default.conf svn.test.com.conf`
* `vi svn.test.com.conf`


```apache
<VirtualHost *:80>
        ServerName svn.test.com
        <Location /svn>  
            DAV svn
            SVNParentPath /home/svn 
            AuthType Basic
            AuthName "SC Project Svn"
            AuthUserFile "/etc/subversion/passwd"  
            AuthzSVNAccessFile "/etc/subversion/authz" 
            Require valid-user
        </Location>

ErrorLog "/var/log/apache2/error-svntest.log"
CustomLog "/var/log/apache2/access-svntest.log" combined
</VirtualHost>
```

* `a2ensite svn.test.com.conf`
* `apachectl restart`

### 添加用户和权限

用http访问方式请注意用命令行生成：

`htpasswd -cm /etc/subversion/passwd  adduser`然后跟密码。
**注意：**以上命令第一次时带-cm参数，第二个生成时不用带-c，否则会覆盖。
`htpasswd -m /etc/subversion/passwd test`

创建完后`vi /etc/subversion/authz`权限文件


```config
[groups]
admin = test /*自己创建*/

[/]
* = r

[repos:/]
@admin = rw

* = r
```







