FROM xutl/base-php:ubuntu-16.04

LABEL maintainer="xutongle@gmail.com"

# persistent / runtime deps
ENV PHPIZE_DEPS autoconf dpkg-dev file g++ gcc libc-dev libpcre3-dev make pkg-config re2c

RUN apt-get update && apt-get install -y $PHPIZE_DEPS ca-certificates curl libedit2 libsqlite3-0 libxml2 libmpfr-dev \
    libpcre3-dev libcurl4-openssl-dev libedit-dev libsqlite3-dev libssl-dev libicu-dev libfreetype6-dev libjpeg-dev \
    libpnglite-dev libsasl2-dev libmhash-dev libevent-dev libjpeg-dev libpng-dev libtidy-dev libxslt-dev \
    libmemcached-dev libbz2-dev libxml2-dev libgmp-dev libyaml-dev libmcrypt-dev --no-install-recommends && \
    rm -r /var/lib/apt/lists/*

ARG PHP_VERSION

# Environment settings
ENV PHP_VERSION ${PHP_VERSION:-5.6.32}
ENV PHP_TGZ_URL http://php.net/distributions/php-${PHP_VERSION}.tar.gz
ENV PHP_INI_DIR /usr/local/etc
ENV PATH=/root/.composer/vendor/bin:$PATH \
    TERM=linux \
    COMPOSER_ALLOW_SUPERUSER=1

# Apply stack smash protection to functions using local buffers and alloca()
# Make PHP's main executable position-independent (improves ASLR security mechanism, and has no performance impact on x86_64)
# Enable optimization (-O2)
# Enable linker optimization (this sorts the hash buckets to improve cache locality, and is non-default)
# Adds GNU HASH segments to generated executables (this is used if present, and is much faster than sysv hash; in this configuration, sysv hash is also generated)
# https://github.com/docker-library/php/issues/272
ENV PHP_CFLAGS="-fstack-protector-strong -fpic -fpie -O2"
ENV PHP_CPPFLAGS="$PHP_CFLAGS"
ENV PHP_LDFLAGS="-Wl,-O1 -Wl,--hash-style=both -pie"

RUN cd /usr/local/src && \
    export CFLAGS="$PHP_CFLAGS" \
    CPPFLAGS="$PHP_CPPFLAGS" \
    LDFLAGS="$PHP_LDFLAGS" && \
    curl -o php-${PHP_VERSION}.tar.gz ${PHP_TGZ_URL} && \
    tar zxf php-${PHP_VERSION}.tar.gz && rm -f php-${PHP_VERSION}.tar.gz && cd php-${PHP_VERSION} && /sbin/ldconfig && \
    if [ ! -f /usr/include/gmp.h ]; then \
        ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h; \
    fi && \
    ./configure --prefix=/usr/local --libdir=/usr/local/lib64 --with-config-file-path=${PHP_INI_DIR} --with-config-file-scan-dir=${PHP_INI_DIR}/php \
    --with-libxml-dir --with-openssl --with-kerberos --with-zlib --enable-bcmath --with-bz2 --enable-calendar --with-curl \
    --enable-exif --disable-cgi --enable-fpm --enable-ftp --with-png-dir --with-gd --with-jpeg-dir  --enable-gd-native-ttf \
    --enable-mbstring --enable-mbregex --enable-shmop --enable-soap --enable-sockets --enable-sysvmsg \
    --enable-sysvsem --enable-sysvshm --enable-wddx --with-xmlrpc --with-libedit --with-iconv-dir=/usr/local --with-xsl \
    --enable-zip --with-pcre-regex --with-freetype-dir --enable-xml  --with-mysqli=mysqlnd \
    --with-pdo-mysql=mysqlnd --with-pdo-sqlite --with-sqlite3 --disable-rpath --enable-inline-optimization  --with-mcrypt \
    --with-mhash --enable-pcntl --without-pear --with-gettext --enable-fileinfo --enable-intl --enable-opcache --enable-cli \
    --with-gmp --with-tidy --with-pcre-dir --with-readline --with-zlib-dir --with-libdir=lib64 && \
    make ZEND_EXTRA_LIBS='-liconv' &&  make install && rm -rf /usr/local/src/php-${PHP_VERSION} && \
    mkdir -p ${PHP_INI_DIR}/php && mkdir -p /var/lib/php

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer && php -r "unlink('composer-setup.php');"

# Add configuration files
COPY image-files/ /

EXPOSE 9000
CMD ["php-fpm"]