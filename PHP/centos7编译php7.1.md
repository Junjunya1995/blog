````
yum -y install libjpeg libjpeg-devel libpng libpng-devel \
freetype freetype-devel \
libxml2 libxml2-devel \
zlib zlib-devel \
curl curl-devel \
ncurses ncurses-devel

yum install openssl openssl-devel

yum install bzip2 bzip2-devel
````

````
./configure \
--prefix=/usr/local/php71 \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-config-file-path=/usr/local/php71/etc \
--with-config-file-scan-dir=/usr/local/php71/etc/php.d \
--with-mcrypt=/usr/local/libmcrypt \
--with-png-dir=/usr/local/libpng \
--with-jpeg-dir \
--with-freetype-dir \
--with-bz2 \
--with-curl \
--with-gd \
--with-mhash \
--with-openssl \
--with-zlib \
--with-gettext \
--with-xmlrpc \
--enable-mysqlnd \
--enable-bcmath \
--enable-fpm \
--enable-bcmath \
--enable-calendar \
--enable-exif \
--enable-ftp \
--enable-mbstring \
--enable-pcntl \
--enable-soap \
--enable-sockets \
--enable-zip

````