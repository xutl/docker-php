FROM xutl/php:7.1-fpm-base

LABEL maintainer="xutongle@gmail.com"

ARG NGINX_VERSION

# Environment settings
ENV NGINX_VERSION=${NGINX_VERSION:-1.13.9}
ENV NGINX_TGZ_URL http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz
ENV NGINX_INI_DIR=/usr/local/etc/nginx

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

# Add configuration files
COPY image-files/ /

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

CMD ["supervisord-foreground"]