FROM xutl/base-php:xenial

LABEL maintainer="xutongle@gmail.com"

ARG PHP_VERSION

# Environment settings
ENV PHP_VERSION=${PHP_VERSION:-5.6.33}
ENV PHP_TGZ_URL=http://cn2.php.net/distributions/php-${PHP_VERSION}.tar.gz \
	PHP_INI_DIR=/usr/local/etc \
	PATH=/root/.composer/vendor/bin:$PATH \
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
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests mcrypt $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& export CFLAGS="$PHP_CFLAGS" \
		CPPFLAGS="$PHP_CPPFLAGS" \
		LDFLAGS="$PHP_LDFLAGS" \
	&& if [ ! -f /usr/include/gmp.h ]; then \
		ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h; \
	fi \
	&& cd /usr/local/src \
	&& curl -fSL ${PHP_TGZ_URL} -o php-${PHP_VERSION}.tar.gz \
	&& tar zxf php-${PHP_VERSION}.tar.gz \
	&& rm -f php-${PHP_VERSION}.tar.gz \
	&& cd php-${PHP_VERSION} \
	&& /sbin/ldconfig \
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
		--with-xmlrpc \
		--with-libedit \
		--with-iconv-dir=/usr/local \
		--with-xsl \
		--with-mysqli=mysqlnd \
		--with-pdo-mysql=mysqlnd \
		--with-pdo-sqlite \
		--with-sqlite3 \
		--with-mhash \
		--with-mcrypt \
		--without-pear \
		--with-gettext \
		--with-gmp \
		--with-tidy \
		--with-pcre-dir \
		--with-readline \
		--with-pcre-regex \
		--with-freetype-dir \
		--with-zlib-dir \
		--with-libdir=lib64 \
		--enable-bcmath \
		--enable-calendar \
		--enable-exif \
		--disable-cgi \
		--enable-ftp \
		--enable-gd-native-ttf \
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
		--enable-xml  \
		--disable-rpath \
		--enable-inline-optimization  \
		--enable-pcntl \
		--enable-fileinfo \
		--enable-intl \
		--enable-opcache \
		--enable-cli \
	&& make -j "$(nproc)" ZEND_EXTRA_LIBS='-liconv' \
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

CMD ["php", "-a"]