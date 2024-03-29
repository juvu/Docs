# Apache

WEB服务器

* 基于文件配置
* 跨平台的,所有操作系统都能安装
* 虚拟主机
* 动态模块加载 能够在无需重新编译主服务器文件的基础上，将模块编译并添加到 Apache 扩展中
*  .htaccess ：使其成为共享托管技术的首选方案，因为 .htaccess 重写支持在目录级别上控制服务器配置。在 Apache 服务器上的每个目录都能够配置自己的 .htaccess 文件。

## 文件结构

* Apache2:程序目录
* bin:二进制文件 httpd
* conf:配置文件
    - httpd.conf
* htdocs:默认网站根目录
* log:日志目录
    - 通过Analog或Webalizer等实用程序访问日志文件
* modules:加载的第三方插件
* Apache2 Service Monitor:监控apache2服务

## 安装

* windows
    - apache2运行于系统服务中。
    - 将bin目录添加到系统变量：Path中添加 apache2/bin(需要重启命令行客户端)
* mac:系统本身带有apache2
    - 程序位置：/private/etc/apache2 或者 /etc/apache2
    - 配置文件：httpd.conf
    - 开启 `LoadModule php7_module libexec/apache2/libphp7.so`
    - 站点默认目录：`DocumentRoot "/Library/WebServer/Documents"`
    - `DirectoryIndex index.php index.html`
    - 进程管理,用httpd或者aapachectl
    - 模块路径： /usr/libexec/apache2

```sh
httpd -t # 检测配置语法
net start apache2 # 服务管理
net stop apache2

apachectl -v
httpd -t
sudo httpd -k start
sudo httpd -k stop
sudo apachectl -k restart

## ubuntu

sudo apt-get install python-software-properties
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install apache2 libapache2-mod-php7.2 mysql-server php7.2 php7.2-xml php7.2-gd php7.2-opcache php7.2-mbstring php7.2-mysql

sudo mkdir -p /var/www/unixmen1.local/public_html
sudo chown -R $USER:$USER /var/www/unixmen1.local/public_html/
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/unixmen1.local.conf

<VirtualHost *:80>
# The ServerName directive sets the request scheme, hostname and port that
# the server uses to identify itself. This is used when creating
# redirection URLs. In the context of virtual hosts, the ServerName
# specifies what hostname must appear in the request's Host: header to
# match this virtual host. For the default virtual host (this file) this
# value is not decisive as it is used as a last resort host regardless.
# However, you must set it for any further virtual host explicitly.
#ServerName www.example.com

ServerAdmin webmaster@unixmen2.local
ServerName unixmen2.local
ServerAlias www.unixmen2.local
DocumentRoot /var/www/unixmen2.local/public_html

# Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
# error, crit, alert, emerg.
# It is also possible to configure the loglevel for particular
# modules, e.g.
#LogLevel info ssl:warn

ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined

# For most configuration files from conf-available/, which are
# enabled or disabled at a global level, it is possible to
# include a line for only one particular virtual host. For example the
# following line enables the CGI configuration for this host only
# after it has been globally disabled with "a2disconf".
#Include conf-available/serve-cgi-bin.conf
</VirtualHost>

sudo service apache2 restart
sudo vi /etc/hosts

# tomcat服务我影响Apache 服务开启
```

### 配置

httpd.conf文件为服务器的主配置文件

* DocumentRoot：网站的根目录，一般绝对路径  `DocumentRoot："E:\www\local"`
* DirectoryIndex：指定网站的默认首页 `DirectoryIndex index.html index.htm index.php`
* Apache启动后，会监听自己电脑的网卡的IP(或端口)的访问
    - listen 80   监听自己电脑的所有IP的80端口的请求
    - listen 192.168.21.80:80   监听自己电脑的指定IP的指定端口的访问。
* `<Directory></Directory>`:默认为所有目录配置的访问权限
    - Options:指定开放哪些服务器特性
        + All：开放所有权限。
        + None：不开放任何权限，任何人都无权访问。
        + Indexes：如果指定的首页文件名(DirectoryIndex)不存在，则会显示格式化目录列表。
        + FollowSymLinks：表示支持符号链接
    - Order:deny allow同时出现，指定禁止和允许的顺序，后面的重写前面的
        + Order Deny,Allow   先禁止，后允许
        + Order Allow,Deny   先允许，后禁止
    - Deny:禁止哪些外部IP的访问
        + Deny from All    //禁止所有IP的访问
        + Deny from 192.168.21.89   //禁止192.168.21.89的访问
        + Deny from 192.168.21     //禁止了整个网段
        + Deny from 192.168.21.89  192.168.21.34 //同时禁止这个IP访问
    - allow:允许哪些外部IP的访问
        + Allow from All
        + Allow from 192.168.21.89
        + Allow from 192.168.21.89  192.168.21.34
* 在apache配置中添加PHP模块，PHP模块不用单独启动，也不能单独停止，一般情况下，是Apache启动时，PHP5模块也跟着启动了。

```
# ubuntu 18.04 don't do this
sudo apt install libapache2-mod-php

AddHandler application/x-httpd-php .php #  AddHandler handler_name extension1 extension2 如果你预览的文件是.php后缀，去调用PHP处理器
AddType application/x-httpd-php .php .html # 添加文件类型和扩展名之间的映射关系
LoadModule php7_module "C:/php7/php7apache2_4.dll" # LoadModule   module_name   module_path
PHPiniDir "D:/wamp/bin/php/php7.0.0" # 指定PHP配置路径，不写可能启动不了 Apache 服务

httpd.exe -M # 查看apache加载了哪些模块

# 文件访问权限的问题
<Directory />
    Options FollowSymLinks
    AllowOverride None
    Order deny,allow
    allow from all
</Directory>
```

### 重写去除入口文件index.php

* 在APACHE里面去配置mod_rewrite.so模块 `#LoadModule rewrite_module modules/mod_rewrite.so`把前面的警号去掉
* AllowOverride None都改为AllowOverride All
* 添加.htaccess文件

```
<IfModule mod_rewrite.c>
    Options +FollowSymlinks
    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
</IfModule>
```

### 分布式配置文件

* 当主服务器配置文件没有进行相应的访问控制时才需要使用 .htaccess 文件
* 在.htaccess配置文件中加入的配置信息只会影响.htaccess所在目录以及子目录的运行效果.
* 不用重启服务器
* 在没有权限修改apache的配置文件或者php的配置文件时可以使用.htaccess来修改Apache原本的配置
* 需要尽可能避免使用 .htaccess 文件。当需要使用 .htaccess 文件时，都可以在主服务器配置的 directory 配置节点去执行配置
* 修改PHP配置信息

```
# 禁用重写功能
/etc/apache2/apache2.conf 
AllowOverride None

php_flag session.auto_start 1 # 指定开关类的配置信息
php_value date.timezone PRC # 指定字符串形式的配置信息

codeRewriteEngine On
RewriteBase /
RewriteCond %{HTTPS} !=on
RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

RewriteEngine on
RewriteBase /
RewriteCond %{SERVER_PORT} !^443$
RewriteRule (.*) https://%{SERVER_NAME}/$1 [R=301,L]
```

#### 虚拟主机

同一个主机上虚拟出来的网站.

* 配置了其他的虚拟主机，默认的主机就会改变为第一个虚拟主机。虚拟主机的优先级最高，比全局配置中的虚拟主机优先级要高。将默认主机作为一个虚拟主机放在第一个虚拟主机的位置即可。第1个虚拟主机的优先最高，localhost或127.0.0.1都会指向它。
* 如果在hosts文件指了域名，但在虚拟主机中并未配置这个域名，此时，将指向第1个虚拟主机。
* 单独再配置一个localhost的虚拟主机，并指向原来的目录。
* 步骤
    - 配置host文件
    - 修改httpd.conf文件包含extra\httpd-vhosts.conf
    - 添加httpd-vhosts.conf中的配置

```
NameVirtualHost *:80

# 配置localhost虚拟主机
<VirtualHost *:80>
    #指定服务器的域名
    ServerName localhost
    #指定网站根目录
    DocumentRoot "e:/www"
    #指定目录权限
    <Directory "e:/www">
        Options Indexes
        Order Deny,Allow
        Deny from All
        Allow from All
    </Directory>
</VirtualHost>

# 配置www.2015.com虚拟主机
<VirtualHost *:80>
    #指定服务器的域名
    ServerName www.2015.com
    #指定网站根目录
    DocumentRoot "e:/itcast/20151101/lesson/day1"
    #指定目录权限
    <Directory "e:/itcast/20151101/lesson/day1">
        Options Indexes
        Order Deny,Allow
        Deny from All
        Allow from All
    </Directory>
    # 访问www.2015.com/music
    Alias /music “e:/itcast/20151101/music”
    <Directory "e:/itcast/20151101/lesson/day1">
        Options Indexes
        Order Deny,Allow
        Deny from All
        Allow from All
    </Directory>
</VirtualHost>
```

### php配置

* 添加PHP程序文件路径到环境变量
* 配置文件php.ini，已存在php.ini-development 和 php.ini-production,复制一份

```
extension_dir = "./" # 模块路径,确保找到文件
extension=php_mysql.dll
php -m # 可以找到mysql
```

### 数据库

* 程序路径
* 数据库路径
* 详细配置、标准配置
* 开发、服务器、只做数据库
* 做多功能、实物型、非事物型
* 并发数选择：OLAP OLTP 或手动设置
* 开启TCP/IP端口。使用Strict Mode（可以做服务器使用）
* 字符集的选择：utf-8
* 添加MySQL后台服务于环境变量
* 用户与密码设置
* 生成配置文件

```
# 服务管理
net start mysql
net stop mysql
```

## 运行模式

* [MPM(mpm_prefork_module) Prefork](http://httpd.apache.org/docs/2.4/mod/prefork.html)：最古老最传统，性能最差但也是最香的模式
    - HTTP 请求->TCP 连接->重新生成一个新进程并响应这个请求。当众多的请求被接收需要创建处理它们的 worker 进程。由于创建新 worker 进程的系统开销巨大，设计了 prefork 模式，并预先生成多个 worker 进程
    - 将每个进程嵌入到动态语言的解释器（如 mod_php）中依然造成大量的资源消耗，这使得 Apache 服务器经常会出现 服务器崩溃 的问题。这是因为单个 worker 进程只能同时处理一个连接
    - [配置](http://httpd.apache.org/docs/current/mod/mpm_common.html)：将 MaxRequestWorkers 指令值配置的足够大，这样可以处理更多的请求，但是还需要保证有每个 worker 进程有足够的物理 RAM 可用
    - Master进程根据配置提前fork出子进程，每个子进程中只有一个线程，然后靠这些子进程干活
    - 一个子进程同时职能干一件事儿，假设说干完一件事需要1秒钟，然后想1秒钟内同时此后10个人的请求，就老老实实开10个进程干活
    - 如果一个进程crash了，并不影响另外九个进程。
    - 引入了两个新的 MPM 模块即 worker 模块 或曰 mpm_worker_module 以及 event 模块。
* Worker模式：一种混合了进程 - 线程（process-thread）处理模式
    - Master进程fork出N个子进程，但是每个子进程内部不再是单线程了，而是多线程，处理具体每一个http请求就由线程来负责处理
    - 单个进程（父进程）负责启动子进程（worker 进程）
    - 子进程负责创建由 ThreadsPerChild 指令设置的服务器线程，同时还负责监听接收到的请求，并将请求分发给处理线程。
    - 优点：节省内存资源和CPU资源，因为N个线程可以共享一个进程的内存地址空间，而且线程间切换耗费CPU也要比进程切换耗费CPU少很多
    - 缺点：要考虑多线程编程安全的问题，因为线程共享进程的内存空间和数据，所以会带来线程安全问题
* [Worker event](http://httpd.apache.org/docs/2.4/mod/event.html)：靠事件监听混饭吃的春药模式，本质上就是要利用epoll搞
    - 2.4 版本 Apache 引入了 - event 模块,基于 worker 模块创建的，并加入了独立的监听线程来管理 HTTP 请求处理完成后的休眠的 keepalive 连接。它是一种异步非阻塞模型，内存占用小
    - 进程和线程模型和Worker模式一模一样，理论上说由于利用事件监听，会更加节省资源，而且对于keep-alive类型的连接，不会存在进程或线程被长期霸占的问题，这都是得益于epoll事件监听
    - 重启服务会消耗大量的内存资源。MaxRequestWorkers 指令设置最大请求数限制：将 MaxConnectionsPerChild 设置为非零非常重要，它可以防止内存泄露
* 前两种模式，都有一个缺点就是keep-alive的连接会长期占据霸占一个进程或者线程，一直到该连接超时，该进程或者线程才会被空闲出来准备接受另外新的http请求。
* PHP解析
    - 默认情况下，PHP解析器是以一个模块形式直接融入到Apache进程中。允许对Apache进程进行改装，往上加载模块
    - CGI方式：
        + 需要解析PHP代码的时候，Apache会通过CGI方式调用一次PHP解析器解析PHP代码，PHP解析器在执行完毕PHP代码后将结果再以CGI的方式返回给Apache，同时，该PHP解析器也会销毁掉
* Apache所采用的select网络I/O模型非常低效:进程干的事情多：执行PHP、输出HTML都得干，占用的资源就多（CPU、内存）

```
# MPM 的 evnet 模块测试
deb http://archive.ubuntu.com/ubuntu xenial main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu xenial-updates main restricted universe multiverse
deb http://security.ubuntu.com/ubuntu xenial-security main restricted universe multiverse
deb http://archive.canonical.com/ubuntu xenial partner

sudo apt-get install libapache2-mod-fastcgi php7.0-fpm
sudo service start php7.0-fpm

# 关闭 prefork 模块，启用 event 模式和 proxy_fcgi
sudo a2dismod php7.0 mpm_prefork
sudo a2enmod mpm_event proxy_fcgi

<filesmatch "\.php$">
    SetHandler "proxy:fcgi://127.0.0.1:9000/"
</filesmatch>

# 调整 mpm_evnet 配置选项 /etc/apache2/mods-available/mpm_event.conf

<ifmodule mpm_event_module>
        StartServers              1
        MinSpareThreads          30
        MaxSpareThreads          75
        ThreadLimit              64
        ThreadsPerChild          30
        MaxRequestWorkers        80
        MaxConnectionsPerChild   80
</ifmodule>
```

## Docker配置

- `mkdir -p  ~/apache/www ~/apache/logs ~/apache/conf`
- Dockerfile

```
FROM debian:jessie

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
#RUN groupadd -r www-data && useradd -r --create-home -g www-data www-data

ENV HTTPD_PREFIX /usr/local/apache2
ENV PATH $PATH:$HTTPD_PREFIX/bin
RUN mkdir -p "$HTTPD_PREFIX" \
    && chown www-data:www-data "$HTTPD_PREFIX"
WORKDIR $HTTPD_PREFIX

# install httpd runtime dependencies
# https://httpd.apache.org/docs/2.4/install.html#requirements
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        libapr1 \
        libaprutil1 \
        libaprutil1-ldap \
        libapr1-dev \
        libaprutil1-dev \
        libpcre++0 \
        libssl1.0.0 \
    && rm -r /var/lib/apt/lists/*

ENV HTTPD_VERSION 2.4.20
ENV HTTPD_BZ2_URL https://www.apache.org/dist/httpd/httpd-$HTTPD_VERSION.tar.bz2

RUN buildDeps=' \
        ca-certificates \
        curl \
        bzip2 \
        gcc \
        libpcre++-dev \
        libssl-dev \
        make \
    ' \
    set -x \
    && apt-get update \
    && apt-get install -y --no-install-recommends $buildDeps \
    && rm -r /var/lib/apt/lists/* \
    \
    && curl -fSL "$HTTPD_BZ2_URL" -o httpd.tar.bz2 \
    && curl -fSL "$HTTPD_BZ2_URL.asc" -o httpd.tar.bz2.asc \
# see https://httpd.apache.org/download.cgi#verify
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys A93D62ECC3C8EA12DB220EC934EA76E6791485A8 \
    && gpg --batch --verify httpd.tar.bz2.asc httpd.tar.bz2 \
    && rm -r "$GNUPGHOME" httpd.tar.bz2.asc \
    \
    && mkdir -p src \
    && tar -xvf httpd.tar.bz2 -C src --strip-components=1 \
    && rm httpd.tar.bz2 \
    && cd src \
    \
    && ./configure \
        --prefix="$HTTPD_PREFIX" \
        --enable-mods-shared=reallyall \
    && make -j"$(nproc)" \
    && make install \
    \
    && cd .. \
    && rm -r src \
    \
    && sed -ri \
        -e 's!^(\s*CustomLog)\s+\S+!\1 /proc/self/fd/1!g' \
        -e 's!^(\s*ErrorLog)\s+\S+!\1 /proc/self/fd/2!g' \
        "$HTTPD_PREFIX/conf/httpd.conf" \
    \
    && apt-get purge -y --auto-remove $buildDeps

COPY httpd-foreground /usr/local/bin/

EXPOSE 80
CMD ["httpd-foreground"]
```
- COPY httpd-foreground /usr/local/bin/ 是将当前目录下的httpd-foreground拷贝到镜像里，作为httpd服务的启动脚本,本地创建一个脚本文件httpd-foreground
```
#!/bin/bash
set -e

# Apache gets grumpy about PID files pre-existing
rm -f /usr/local/apache2/logs/httpd.pid
```
- chmod +x httpd-foreground
- docker build -t httpd .
- docker run -p 80:80 -v $PWD/www/:/usr/local/apache2/htdocs/ -v $PWD/conf/httpd.conf:/usr/local/apache2/conf/httpd.conf -v $PWD/logs/:/usr/local/apache2/logs/ -d httpd

### 添加phpmyadmin

- 添加虚拟主机
- 重启web服务器服务
- 访问 `http://localhost/phpmyadmin`

```
Alias /phpmyadmin /usr/local/share/phpmyadmin
  <Directory /usr/local/share/phpmyadmin/>
    Options Indexes FollowSymLinks MultiViews
    AllowOverride All
    <IfModule mod_authz_core.c>
      Require all granted
    </IfModule>
    <IfModule !mod_authz_core.c>
      Order allow,deny
      Allow from all
    </IfModule>
  </Directory>

[2002] No such file or directory  # 登陆 填入用户名密码却提示
sudo mkdir /var/mysql
sudo ln -s /private/tmp/mysql.sock /var/mysql/mysql.sock
```

### apache + mod_fastcgi

```sh
brew tap homebrew/apache
brew install httpd24
brew install mod_fastcgi --with-brewed-httpd24

# 编辑 /usr/local/etc/apache2/2.4/httpd.conf
LoadModule fastcgi_module /usr/local/opt/mod_fastcgi/libexec/mod_fastcgi.so

<IfModule fastcgi_module>
    ProxyPassMatch ^/(.*\.php(/.*)?)$
    unix:/usr/local/var/run/php-fpm.sock|fcgi://127.0.0.1:9000/usr/local/var/www/htdocs
</IfModule>
```

##  集成环境

* LAMP for Linux
* MAMP for Mac
* SAMP for Solaris
* FAMP for FreeBSD
* XAMPP (Cross, Apache, MySQL, PHP, Perl) 对于跨平台：它还包括一些其他组件，如FileZilla，OpenSSL，Webalizer，OpenSSL，Mercury Mail等。
* [WAMPSERVER](http://www.wampserver.com/en/):https://sourceforge.net/projects/wampserver/files/WampServer%203/WampServer%203.0.0/

## 参考

* [howtolamp](http://howtolamp.com/)
