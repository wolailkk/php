####升级PHP版本（5.6现要迁移到7.2追上时代的脚步）
#####文章学习来源：https://blog.csdn.net/gocuber/article/details/80613601
######https://www.cnblogs.com/xjnotxj/p/6125305.html
######
######在升级过成功遇到几次缺少依赖及扩展的安装问题
######还有一个用户权限问题
######在make install好了后竟然etc下没有.ini后来从源码包中CP过来一个用的
####php-fpm72 几个参数 start|stop|force-quit|restart|reload|status|configtest
```angular2html
service php-fpm72 start       # 启动服务
service php-fpm72 stop        # 停止服务
service php-fpm72 restart     # 重启服务
service php-fpm72 reload      # 平滑重启服务
service php-fpm72 force-quit  # 强制退出服务
service php-fpm72 status      # 查看服务状态
service php-fpm72 configtest  # 检查配置
```
```
安装一些依赖否则一会还是要装(这里一般是用的频次多的，在编译过程还是会缺少部分依赖)
yum groupinstall "Development Tools"
yum install libxml2-devel openssl-devel libcurl-devel gd-devel gmp-devel libicu-devel recode recode-devel libxslt libxslt-devel
```
####安装PHP-7.2.6
官网 php.net 下载的：php-7.2.6.tar.gz
```
 cd /usr/local
 tar zxvf php-7.2.6.tar.gz
 cd php-7.2.6/
 
 ./configure \
 --prefix=/usr/local/php72 \
 --with-config-file-path=/usr/local/php72/etc \
 --disable-debug \
 --disable-phpdbg \
 --enable-mysqlnd \
 --enable-bcmath \
 --with-bz2=/usr \
 --enable-calendar \
 --with-curl \
 --enable-exif \
 --enable-fpm \
 --with-fpm-user=www \
 --with-fpm-group=www \
 --with-freetype-dir \
 --enable-ftp \
 --with-gd \
 --enable-gd-jis-conv \
 --with-gettext=/usr \
 --with-gmp \
 --with-iconv \
 --with-iconv-dir=/usr/local/libiconv \
 --enable-intl \
 --with-jpeg-dir \
 --enable-mbstring \
 --with-openssl \
 --with-mhash \
 --enable-pcntl \
 --with-pdo-mysql=mysqlnd \
 --with-png-dir \
 --with-recode \
 --enable-shmop \
 --enable-soap \
 --enable-sockets \
 --enable-sysvmsg \
 --enable-sysvsem \
 --enable-sysvshm \
 --enable-wddx \
 --with-xmlrpc \
 --with-xsl \
 --with-zlib=/usr \
 --enable-zip \
 --with-libxml-dir=/usr \
 --enable-xml \
 --with-mysqli=mysqlnd \
 --enable-fileinfo \
 --enable-opcache \
 --with-libdir=lib64 \
 --enable-maintainer-zts
 -----------------------------------------------------------------------
 # --enable-opcache            # PHP7.2 相对于 PHP7.1 主要优化 opcache
 # --with-libdir=lib64         # 64位系统需指定 lib64 32位系统去掉此项
 # --enable-maintainer-zts     # ZTS 线程安全，默认 NTS 非线程安全
 # make clean   # 如果之前编译过 需要清除上次的编辑结果
 make
 make install
 
 cp php.ini-development /usr/local/php72/etc/php.ini
```
####修改php.ini配置(这里我就没生成php.ini，自己去源码里拷贝的)
vim /usr/local/php72/etc/php.ini
```
# 找到 ;cgi.fix_pathinfo=1 修改为
cgi.fix_pathinfo=0

# 找到 ;date.timezone = 修改为
date.timezone = PRC
```
####查看版本
```/usr/local/php72/bin/php -v```
####配置php-fpm
#####修改php-fpm配置文件
```
cp /usr/local/php72/etc/php-fpm.conf.default /usr/local/php72/etc/php-fpm.conf

vi /usr/local/php72/etc/php-fpm.conf

# 找到 ;pid = run/php-fpm.pid 修改为

pid = /usr/local/php72/var/run/php-fpm.pid
```
#####修改www.conf
```angular2html
cp /usr/local/php72/etc/php-fpm.d/www.conf.default /usr/local/php72/etc/php-fpm.d/www.conf
vi /usr/local/php72/etc/php-fpm.d/www.conf

user = www
group = www


# 找到 listen = 127.0.0.1:9000 如果启了php5 或其它 9000端口被占用的话 修改为

listen = 127.0.0.1:9001
```
#####配置 php-fpm 启动脚本 注册为系统服务 设置开机启动
```angular2html
cp /usr/local/php-7.2.6/sapi/fpm/php-fpm /usr/local/php72/bin
cp /usr/local/php-7.2.6/sapi/fpm/init.d.php-fpm /etc/init.d/php-fpm72
```
#####将/etc/init.d/php-fpm72开始的注释部分php-fpm改为php-fpm72 虽然是注释，但在Linux下注册系统服务要求的格式；如果你不注册为系统服务则不需要改；
######vi /etc/init.d/php-fpm72
```angular2html
#! /bin/sh
### BEGIN INIT INFO
# Provides:          php-fpm72    <------------------这里
# Required-Start:    $remote_fs $network
# Required-Stop:     $remote_fs $network
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: starts php-fpm72
# Description:       starts the PHP FastCGI Process Manager daemon
### END INIT INFO
```
####执行权限
```chmod +x /etc/init.d/php-fpm72```
####添加php-fpm72至服务列表并设置开机自启
```angular2html
chkconfig --add php-fpm72
chkconfig --list php-fpm72
chkconfig php-fpm72 on
```
####启动php-fpm72服务
```angular2html
service php-fpm72 configtest   # 检查配置 显示如下 说明配置没有问题

# [07-Jun-2018 11:53:45] NOTICE: configuration file /usr/local/php72/etc/php-fpm.conf test is successful

service php-fpm72 start        # 启动

# Starting php-fpm  done 启动成功
```
####如果启动失败并提示用户错误的话
```angular2html
查看/usr/local/php72/etc/php-fpm.d/www.conf
找到user修改成如下就可
user = nobody
group = nobody

```
####利用ps -ef 查看php-fpm进程
```angular2html
ps -ef |grep php

root      1805     1  0 09:48 ?        00:00:00 php-fpm: master process (/usr/local/php56/etc/php-fpm.conf)                                                                                        
nobody    1806  1805  0 09:48 ?        00:00:00 php-fpm: pool www                                                                                                                                          
nobody    1807  1805  0 09:48 ?        00:00:00 php-fpm: pool www                                                                                                                                          
root     31580     1  0 11:54 ?        00:00:00 php-fpm: master process (/usr/local/php72/etc/php-fpm.conf)                                                                        
www      31581 31580  0 11:54 ?        00:00:00 php-fpm: pool www                                                                                                                  
www      31582 31580  0 11:54 ?        00:00:00 php-fpm: pool www                                                                                                                  
root     31585  3311  0 11:54 pts/1    00:00:00 grep php
```
######后面根据nginx配置绑定监听端口号就好了










