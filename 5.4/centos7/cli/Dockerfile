FROM centos:7

MAINTAINER XUTONGLE <xutongle@gmail.com>

RUN set -xe && \
    echo -e "/lib64\n/usr/lib64\n/usr/local/lib64\n/lib\n/usr/lib\n/usr/local/lib\n`cat /etc/ld.so.conf`" > /etc/ld.so.conf && \
    /sbin/ldconfig && yum update -y && yum install -y patch make cmake gcc gcc-c++ bison flex file libtool \
    libtool-libs autoconf re2c libaio libcap glibc glib2 kernel-devel glibc-devel glib2-devel curl unzip epel-release && \
    yum install -y diffutils ncurses ncurses-devel libjpeg libpng freetype libxml2 zlib bzip2 libidn gettext libxslt libedit \
    libtidy gd libyaml libxml2-devel openssl-devel bzip2-devel curl-devel libjpeg-devel libpng-devel freetype-devel \
    cyrus-sasl-devel gmp-devel libedit-devel libtidy-devel libxslt-devel readline-devel libidn-devel zlib-devel  \
    gettext-devel gd-devel libyaml-devel mhash mhash-devel mcrypt libmcrypt libmcrypt-devel libmemcached libmemcached-devel \
    libicu libicu-devel && yum clean all && rm -rf /var/cache/yum/* && \
    /usr/sbin/groupadd -r www && groupmod -g 1000 www && /usr/sbin/useradd -r -M -g www www && usermod -u 1000 www

RUN cd /usr/local/src && curl -o libiconv-1.15.tar.gz http://7xk96f.com1.z0.glb.clouddn.com/software/libiconv/libiconv-1.15.tar.gz && \
    tar zxf libiconv-1.15.tar.gz && rm -rf libiconv-1.15.tar.gz && cd libiconv-1.15/ && \
    ./configure --prefix=/usr/local --libdir=/usr/local/lib64 --enable-static && \
    make && make install && rm -rf /usr/local/src/libiconv-1.15

RUN cd /usr/local/src && curl -o icu4c-58_2-src.tgz http://7xk96f.com1.z0.glb.clouddn.com/software/icu/icu4c-58_2-src.tgz && \
    tar zxf icu4c-58_2-src.tgz && rm -rf icu4c-58_2-src.tgz && cd icu/source/ && \
    ./configure --prefix=/usr/local --libdir=/usr/local/lib64 --enable-static && \
    make && make install && /sbin/ldconfig

ARG PHP_VERSION=5.4.45

ENV PHP_INI_DIR=/usr/local/etc

RUN cd /usr/local/src && curl -L -o php-${PHP_VERSION}.tar.gz http://cn2.php.net/get/php-${PHP_VERSION}.tar.gz/from/this/mirror && \
    tar zxf php-${PHP_VERSION}.tar.gz && rm -f php-${PHP_VERSION}.tar.gz && cd php-${PHP_VERSION} && \
    /sbin/ldconfig && ./configure --prefix=/usr/local --libdir=/usr/local/lib64 --with-config-file-path=${PHP_INI_DIR} --with-config-file-scan-dir=${PHP_INI_DIR}/php \
    --with-libxml-dir --with-openssl --with-kerberos --with-zlib --enable-bcmath --with-bz2 --enable-calendar --with-curl \
    --enable-exif --disable-cgi --enable-ftp --with-png-dir --with-gd --with-jpeg-dir --enable-gd-native-ttf \
    --with-icu-dir=/usr/local --enable-mbstring --enable-mbregex --enable-shmop --enable-soap --enable-sockets \
    --enable-sysvmsg --enable-sysvsem --enable-sysvshm --enable-wddx --with-xmlrpc --with-libedit --with-iconv-dir=/usr/local \
    --with-xsl --enable-zip --with-pcre-regex --with-freetype-dir --enable-xml  --with-mysqli=mysqlnd \
    --with-pdo-mysql=mysqlnd --with-pdo-sqlite --with-sqlite3 --disable-rpath --enable-inline-optimization --with-mcrypt \
    --with-mhash --enable-pcntl --without-pear --with-gettext --enable-fileinfo --enable-intl --enable-cli \
    --with-gmp --with-tidy --with-pcre-dir --with-readline --with-zlib-dir --with-libdir=lib64 && \
    make ZEND_EXTRA_LIBS='-liconv' &&  make install && rm -rf /usr/local/src/php-${PHP_VERSION} && \
    mkdir -p ${PHP_INI_DIR}/php && mkdir -p /var/lib/php

ENV MSGPACK_VERSION=0.5.7 \
    IGBINARY_VERSION=2.0.5 \
    MEMCACHED_VERSION=2.2.0 \
    REDIS_VERSION=3.1.4 \
    YAML_VERSION=1.3.1 \
    MEMCACHE_VERSION=3.0.8

RUN cd /usr/local/src && curl -o msgpack-${MSGPACK_VERSION}.tgz http://pecl.php.net/get/msgpack-${MSGPACK_VERSION}.tgz && \
    tar zxf msgpack-${MSGPACK_VERSION}.tgz && rm -rf msgpack-${MSGPACK_VERSION}.tgz && cd msgpack-${MSGPACK_VERSION} && \
    phpize && ./configure && make && make install && rm -rf /usr/local/src/msgpack-${MSGPACK_VERSION} && \
    echo "extension=msgpack.so" >> ${PHP_INI_DIR}/php/msgpack.ini

RUN cd /usr/local/src && curl -o igbinary-${IGBINARY_VERSION}.tgz http://pecl.php.net/get/igbinary-${IGBINARY_VERSION}.tgz && \
    tar zxf igbinary-${IGBINARY_VERSION}.tgz && rm -rf igbinary-${IGBINARY_VERSION}.tgz && cd igbinary-${IGBINARY_VERSION} && \
    phpize && ./configure && make && make install && rm -rf /usr/local/src/igbinary-${IGBINARY_VERSION} && \
    echo "extension=igbinary.so" >> ${PHP_INI_DIR}/php/igbinary.ini

RUN cd /usr/local/src && curl -o memcache-${MEMCACHE_VERSION}.tgz http://pecl.php.net/get/memcache-${MEMCACHE_VERSION}.tgz && \
    tar zxf memcache-${MEMCACHE_VERSION}.tgz && rm -rf memcache-${MEMCACHE_VERSION}.tgz && cd memcache-${MEMCACHE_VERSION} && \
    phpize && ./configure --enable-memcache --with-zlib-dir --with-libdir=lib64 && \
    make && make install && rm -rf /usr/local/src/memcache-${MEMCACHE_VERSION} && \
    echo "extension=memcache.so" >> ${PHP_INI_DIR}/php/memcache.ini

RUN cd /usr/local/src && curl -o memcached-${MEMCACHED_VERSION}.tgz http://pecl.php.net/get/memcached-${MEMCACHED_VERSION}.tgz && \
    tar zxf memcached-${MEMCACHED_VERSION}.tgz && rm -rf memcached-${MEMCACHED_VERSION}.tgz && cd memcached-${MEMCACHED_VERSION} && \
    phpize && ./configure --enable-memcached --enable-memcached-igbinary --enable-memcached-json --enable-memcached-msgpack --with-libdir=lib64 && \
    make && make install && rm -rf /usr/local/src/memcached-${MEMCACHED_VERSION} && \
    echo "extension=memcached.so" >> ${PHP_INI_DIR}/php/memcached.ini

RUN cd /usr/local/src && curl -o redis-${REDIS_VERSION}.tgz http://pecl.php.net/get/redis-${REDIS_VERSION}.tgz && \
    tar zxf redis-${REDIS_VERSION}.tgz && rm -rf redis-${REDIS_VERSION}.tgz && cd redis-${REDIS_VERSION} && \
    phpize && ./configure --enable-redis --enable-redis-igbinary && make && make install && \
    rm -rf /usr/local/src/redis-${REDIS_VERSION} && \
    echo "extension=redis.so" >> ${PHP_INI_DIR}/php/redis.ini

RUN cd /usr/local/src && curl -o yaml-${YAML_VERSION}.tgz http://pecl.php.net/get/yaml-${YAML_VERSION}.tgz && \
    tar zxf yaml-${YAML_VERSION}.tgz && rm -rf yaml-${YAML_VERSION}.tgz && cd yaml-${YAML_VERSION} && \
    phpize && ./configure && make && make install && rm -rf /usr/local/src/yaml-${YAML_VERSION} && \
    echo "extension=yaml.so" >> ${PHP_INI_DIR}/php/yaml.ini

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer && php -r "unlink('composer-setup.php');"

# Add configuration files
COPY image-files/ /

CMD ["php", "-a"]