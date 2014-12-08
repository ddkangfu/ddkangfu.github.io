---
layout: default
title: Mac中的PHP环境
category: 工具
comments: true
---

# Mac中的PHP环境

Mac系统中已经内置了一套Apache+PHP环境，以笔者的Yosemite 10.10.1系统为例：

```
sudo apachectl －v #查看Apache的版本号
Server version: Apache/2.4.9 (Unix)
Server built:   Sep  9 2014 14:48:20

sudo apachectl start #启动Apache
```

默认打开80端口，使用`http://localhost`即可访问。默认的根目录为`/Library/WebServer/Documents`，有权限限制，操作文件时需要加sudo。

>注意：开启了Apache就是开启了“Web共享”，这时联网的用户就会通过“http://[本地IP]/”来访问“/Library（资源库）/WebServer/Documents/”目录，通过“http://[本地IP]/~[用户名]”来访问“/Users/[用户名]/Sites/”目录，可以通过设置“系统偏好设置”的“安全（Security）”中的“防火墙（Firewall）”来禁止这种访问。

修改Apache的配置文件启动PHP:

```
sudo vi /etc/apache2/httpd.conf
```

找到`#LoadModule php5_module libexec/apache2/libphp5.so`并去掉前面的#号后保存。

```
sudo cp /etc/php.ini.default /etc/php.ini
```

然后再配置php.ini。

再使用`sudo apachectl restart`重启Apache，PHP就可以使用了。