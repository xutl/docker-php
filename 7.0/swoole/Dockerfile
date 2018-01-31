FROM xutl/php:7.0-fpm-base

LABEL maintainer="xutongle@gmail.com"

RUN set -xe \
	&& buildDeps=" \
		libnghttp2-dev \
		libssl-dev \
	" \
	\
#	&& sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/' /etc/apt/sources.list \
	&& apt-get update && apt-get install -y --no-install-recommends --no-install-suggests libnghttp2-14 $buildDeps && rm -r /var/lib/apt/lists/* \
	\
	&& msgpackVersion=2.0.2 \
		igbinaryVersion=2.0.5 \
		memcachedVersion=3.0.4 \
		redisVersion=3.1.6 \
		yamlVersion=2.0.2 \
		swooleVersion=2.0.12 \
	\
	&& cd /usr/local/src \
	&& curl -fSL https://github.com/redis/hiredis/archive/v0.13.3.tar.gz -o hiredis-0.13.3.tar.gz \
	&& tar zxf hiredis-0.13.3.tar.gz \
	&& rm -rf hiredis-0.13.3.tar.gz \
	&& cd hiredis-0.13.3 \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/hiredis-0.13.3 \
	\
	&& cd /usr/local/src \
	&& curl -fSL http://pecl.php.net/get/swoole-${swooleVersion}.tgz -o swoole-${swooleVersion}.tgz \
	&& tar zxf swoole-${swooleVersion}.tgz \
	&& rm -rf swoole-${swooleVersion}.tgz \
	&& cd swoole-${swooleVersion} \
	&& phpize \
	&& ./configure --enable-http2 --enable-openssl --enable-sockets --enable-mysqlnd --enable-async-redis \
	&& make -j "$(nproc)" \
	&& make install \
	&& rm -rf /usr/local/src/swoole-${swooleVersion} \
	&& echo "extension=swoole.so" >> ${PHP_INI_DIR}/php/swoole.ini \
	\
	&& apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDeps

CMD ["php", "-a"]