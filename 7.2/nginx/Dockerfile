FROM xutl/base-php:xenial

LABEL maintainer="xutongle@gmail.com"

ARG PHP_VERSION
ARG NGINX_VERSION

# Environment settings
ENV NGINX_VERSION=${NGINX_VERSION:-1.13.8}
ENV NGINX_TGZ_URL http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz
ENV NGINX_INI_DIR=/usr/local/etc/nginx
ENV PHP_VERSION ${PHP_VERSION:-7.2.1}
ENV PHP_TGZ_URL http://php.net/distributions/php-${PHP_VERSION}.tar.gz
ENV PHP_INI_DIR=/usr/local/etc \
	PATH=/root/.composer/vendor/bin:$PATH \
	TERM=linux \
	COMPOSER_ALLOW_SUPERUSER=1

RUN set -xe \
	&& buildDeps=" \
		libpcre3-dev \
		libcurl4-openssl-dev \
		libssl-dev \
		libbz2-dev \
		libxml2-dev \
		libzip-dev \
		libxslt-dev \
		libgd-dev \
	" \
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests libgd3 supervisor $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& cd /usr/local/src \
	&& curl -fSL ${NGINX_TGZ_URL} -o nginx-${NGINX_VERSION}.tar.gz \
	&& tar zxf nginx-${NGINX_VERSION}.tar.gz \
	&& rm -rf nginx-${NGINX_VERSION}.tar.gz \
	&& cd nginx-${NGINX_VERSION}/ \
	&& ./configure \
		--prefix=/usr/local \
		--conf-path=${NGINX_INI_DIR}/nginx.conf \
		--error-log-path=/var/log/nginx/error.log \
		--http-client-body-temp-path=/var/lib/nginx/body \
		--http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
		--http-log-path=/var/log/nginx/access.log \
		--http-proxy-temp-path=/var/lib/nginx/proxy \
		--lock-path=/run/lock/nginx.lock \
		--pid-path=/run/nginx.pid \
		--with-http_ssl_module \
		--with-http_v2_module \
		--with-http_image_filter_module \
		--with-http_slice_module \
		--with-http_xslt_module \
		--with-http_realip_module \
		--with-http_stub_status_module \
		--with-pcre \
		--with-pcre-jit \
		--with-http_flv_module \
		--with-http_mp4_module \
		--with-http_addition_module \
		--with-threads \
		--with-http_secure_link_module \
		--with-http_degradation_module \
		--with-http_ssl_module \
		--with-http_gzip_static_module \
		--without-mail_imap_module \
		--without-mail_pop3_module \
		--without-mail_smtp_module \
		--without-http_uwsgi_module \
		--without-http_scgi_module \
		--without-select_module \
		--with-http_sub_module \
		--with-cc-opt='-O2' \
	&& make -j "$(nproc)" \
	&& make install \
	&& mkdir -p /var/lib/nginx \
	&& rm -rf /usr/local/src/nginx-${NGINX_VERSION} \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

# Apply stack smash protection to functions using local buffers and alloca()
# Make PHP's main executable position-independent (improves ASLR security mechanism, and has no performance impact on x86_64)
# Enable optimization (-O2)
# Enable linker optimization (this sorts the hash buckets to improve cache locality, and is non-default)
# Adds GNU HASH segments to generated executables (this is used if present, and is much faster than sysv hash; in this configuration, sysv hash is also generated)
# https://github.com/docker-library/php/issues/272
ENV PHP_CFLAGS="-fstack-protector-strong -fpic -fpie -O2"
ENV PHP_CPPFLAGS="$PHP_CFLAGS"
ENV PHP_LDFLAGS="-Wl,-O1 -Wl,--hash-style=both -pie"

RUN set -xe \
	&& buildDeps=" \
		libxml2-dev \
		libssl-dev \
		libbz2-dev \
		libcurl4-openssl-dev \
		libjpeg-dev \
		libpng-dev \
		libfreetype6-dev \
		libgmp-dev \
		libmcrypt-dev \
		libedit-dev \
		libtidy-dev \
		libxslt-dev \
		libzip-dev \
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& export CFLAGS="$PHP_CFLAGS" \
		CPPFLAGS="$PHP_CPPFLAGS" \
		LDFLAGS="$PHP_LDFLAGS" \
	&& cd /usr/local/src \
	&& curl -fSL ${PHP_TGZ_URL} -o php-${PHP_VERSION}.tar.gz \
	&& tar zxf php-${PHP_VERSION}.tar.gz \
	&& rm -f php-${PHP_VERSION}.tar.gz \
	&& cd php-${PHP_VERSION} \
	&& ./configure \
		--prefix=/usr/local \
		--libdir=/usr/local/lib64 \
		--with-config-file-path=${PHP_INI_DIR} \
		--with-config-file-scan-dir=${PHP_INI_DIR}/php \
		--with-libxml-dir \
		--with-openssl \
		--with-kerberos \
		--with-zlib \
		--with-bz2 \
		--with-curl \
		--with-png-dir \
		--with-gd \
		--with-jpeg-dir \
		--with-libzip \
		--with-xmlrpc \
		--with-libedit \
		--with-iconv-dir=/usr/local \
		--with-xsl \
		--with-pcre-regex \
		--with-pcre-jit \
		--with-freetype-dir \
		--with-mysqli=mysqlnd \
		--with-pdo-mysql=mysqlnd \
		--with-pdo-sqlite \
		--with-sqlite3 \
		--with-mhash \
		--without-pear \
		--with-gettext \
		--with-gmp --with-tidy \
		--with-pcre-dir \
		--with-readline \
		--with-zlib-dir \
		--with-libdir=lib64 \
		--enable-bcmath \
		--enable-calendar  \
		--enable-exif \
		--disable-cgi \
		--enable-ftp \
		--enable-mbstring \
		--enable-mbregex \
		--enable-shmop \
		--enable-soap \
		--enable-sockets \
		--enable-sysvmsg \
		--enable-sysvsem \
		--enable-sysvshm \
		--enable-wddx \
		--enable-zip \
		--enable-xml \
		--disable-rpath \
		--enable-inline-optimization \
		--enable-pcntl \
		--enable-fileinfo \
		--enable-intl \
		--enable-opcache \
		--enable-cli \
		--enable-fpm \
	&& make ZEND_EXTRA_LIBS='-liconv' \
	&& make install \
	&& rm -rf /usr/local/src/php-${PHP_VERSION} \
	&& mkdir -p ${PHP_INI_DIR}/php \
	&& mkdir -p /var/lib/php \
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

# Add configuration files
COPY image-files/ /

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
	&& php -r "unlink('composer-setup.php');" \
	&& composer global require hirak/prestissimo --optimize-autoloader \
	&& composer global dumpautoload --optimize \
	&& composer clear-cache

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log \
	&& {\
		echo "<?php"; \
		echo "phpinfo();"; \
	} > /usr/local/html/info.php \
	&& chmod 775 /usr/local/html/info.php

EXPOSE 80

HEALTHCHECK --interval=5s --timeout=2s --retries=12 \
  CMD curl --silent --fail localhost:80/ping || exit 1

CMD ["supervisord","-n"]