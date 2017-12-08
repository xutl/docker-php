FROM debian:stretch

MAINTAINER XUTONGLE <xutongle@gmail.com>

# persistent / runtime deps
ENV PHPIZE_DEPS autoconf dpkg-dev file g++ gcc libc-dev libpcre3-dev make pkg-config re2c

RUN apt-get update && apt-get install -y $PHPIZE_DEPS ca-certificates curl libedit2 libsqlite3-0 libxml2 libmpfr-dev \
    libpcre3-dev libcurl4-gnutls-dev libedit-dev libsqlite3-dev libssl-dev libicu-dev libfreetype6-dev libjpeg-dev \
    libpnglite-dev libsasl2-dev libmhash-dev libevent-dev libjpeg62-turbo-dev libpng-dev libtidy-dev libxslt-dev \
    libmemcached-dev libbz2-dev libxml2-dev libgmp-dev libyaml-dev libmcrypt-dev --no-install-recommends && \
    rm -r /var/lib/apt/lists/* && rm -f /etc/ld.so.conf.d/libc.conf && \
    echo -e "/lib64\n/usr/lib64\n/usr/local/lib64\n/lib\n/usr/lib\n/usr/local/lib" > /etc/ld.so.conf.d/libc.conf && \
    /sbin/ldconfig && /usr/sbin/groupadd -r www && groupmod -g 1000 www && /usr/sbin/useradd -r -M -g www www && usermod -u 1000 www

RUN cd /usr/local/src && curl -o libiconv-1.15.tar.gz  https://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.15.tar.gz && \
    tar zxf libiconv-1.15.tar.gz && rm -rf libiconv-1.15.tar.gz && cd libiconv-1.15/ && \
    ./configure --prefix=/usr/local --libdir=/usr/local/lib64 --enable-static && \
    make && make install && rm -rf /usr/local/src/libiconv-1.15

ARG PHP_VERSION=5.4.45

ENV PHP_INI_DIR=/usr/local/etc

RUN cd /usr/local/src && curl -o php-${PHP_VERSION}.tar.gz http://php.net/distributions/php-${PHP_VERSION}.tar.gz && \
    tar zxf php-${PHP_VERSION}.tar.gz && rm -f php-${PHP_VERSION}.tar.gz && cd php-${PHP_VERSION} && /sbin/ldconfig && \
    if [ ! -f /usr/include/gmp.h ]; then \
        ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h; \
    fi && \
    ./configure --prefix=/usr/local --libdir=/usr/local/lib64 --with-config-file-path=${PHP_INI_DIR} --with-config-file-scan-dir=${PHP_INI_DIR}/php \
    --with-libxml-dir --with-openssl --with-kerberos --with-zlib --enable-bcmath --with-bz2 --enable-calendar --with-curl \
    --enable-exif --disable-cgi --enable-ftp --with-png-dir --with-gd --with-jpeg-dir  --enable-gd-native-ttf \
    --enable-mbstring --enable-mbregex --enable-shmop --enable-soap --enable-sockets --enable-sysvmsg \
    --enable-sysvsem --enable-sysvshm --enable-wddx --with-xmlrpc --with-libedit --with-iconv-dir=/usr/local --with-xsl \
    --enable-zip --with-pcre-regex --with-freetype-dir --enable-xml  --with-mysqli=mysqlnd \
    --with-pdo-mysql=mysqlnd --with-pdo-sqlite --with-sqlite3 --disable-rpath --enable-inline-optimization  --with-mcrypt \
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