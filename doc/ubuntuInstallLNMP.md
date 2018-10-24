###Ubuntu安装LNMP环境
######因为ubuntu安装时没有包含openssl所以需要手动安装下
```通过apt-get来安装 : sudo apt install openssh-server```
####安装PHP
```
切换了一下源
sudo apt-get install software-properties-common python-software-properties 
sudo add-apt-repository ppa:ondrej/php && sudo apt-get update
sudo apt-get -y install php7.2

 # 如果之前有其他版本PHP，在这边禁用掉
sudo a2dismod php5
sudo a2enmod php7.2
# 安装常用扩展
sudo -y apt-get install php7.2-fpm php7.2-mysql php7.2-curl php7.2-json php7.2-mbstring php7.2-xml  php7.2-intl 
--------------------- 
知识当时看的这个博主的bloghttps://blog.csdn.net/qq_16885135/article/details/79747045 
```
###PHP版本的控制
```$ sudo a2dismod php5.6 #停用 php5.6
$ sudo a2enmod php7.1 #启用 php7.1
$ sudo service apache2 restart #重启 apache或nginx
$ sudo update-alternatives --set php /usr/bin/php7.1
## 至此已经由 php5.6 切换至 php7.1
```
####安装nginx
```
# 安装 build 所需要的
    sudo apt-get install build-essential
    sudo apt-get install libtool
    apt-get install openssl
    apt-get install libssl-dev
# 安装 pcre zlib 前者为了重写rewrite，后者为了gzip压缩
    <<pcre>>
    cd /usr/local/src
    sudo wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.40.tar.gz
    tar -zxvf pcre-8.40.tar.gz
    cd pcre-8.40
    ./configure
    make
    make install
    -------------------------------------------------------------------------
    <<zlib>>
    cd /usr/local/src
    wget http://zlib.net/zlib-1.2.8.tar.gz
    tar -zxvf zlib-1.2.8.tar.gz
    cd zlib-1.2.8
    ./configure
    make
    make install

    apt-get install build-essential
    apt-get install libtool
    -------------------------------------------------------------------------
    cd /usr/local/src
    sudo wget http://nginx.org/download/nginx-1.12.2.tar.gz
    sudo tar -zxvf nginx-1.12.2.tar.gz
    cd nginx-1.12.2
    ./configure
    make
    make install
    //扩展现只做参考
    //./configure --sbin-path=/usr/local/nginx/nginx \
    //--conf-path=/usr/local/nginx/nginx.conf \
    //--pid-path=/usr/local/nginx/nginx.pid \
    //--with-http_ssl_module \
    //--with-pcre=/opt/app/openet/oetal1/chenhe/pcre-8.37 \
    //--with-zlib=/opt/app/openet/oetal1/chenhe/zlib-1.2.8 \
    //--with-openssl=/opt/app/openet/oetal1/chenhe/openssl-1.0.1t \
    //--with-pcre=/usr/local/src/pcre-8.40 \
    //--with-zlib=/usr/local/src/zlib-1.2.11
```
####安装nginx的时候出现

```
./configure: error: SSL modules require the OpenSSL library. 
```
####常用命令
启动nginx：
```$ sudo /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
# 注意：-c 指定配置文件的路径，不加的话，nginx会自动加载默认路径的配置文件，可以通过 -h查看帮助命令。
$ sudo /usr/local/nginx/sbin/nginx -t #检查配置文件
$ sudo /usr/local/nginx/sbin/nginx -s reload #重启
$ sudo /usr/local/nginx -s stop #停止
#查看nginx进程：
$ ps -ef|grep nginx

service nginx start
service php7.0-fpm start
netstat -nutlp
在遇到nginx绑定php-fpm的时候发现pool.d/www.conf里面绑定的端口并非9000在后没重启成功所以耽误了时间，很大的误会
```
