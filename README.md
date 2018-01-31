# docker-php 

[![Build Status](https://travis-ci.org/xutl/docker-php.svg?branch=master)](https://travis-ci.org/xutl/docker-php) ![](https://img.shields.io/badge/PHP-5.6.32-brightgreen.svg) ![](https://img.shields.io/badge/PHP-7.0.26-brightgreen.svg) ![](https://img.shields.io/badge/PHP-7.1.12-brightgreen.svg) ![](https://img.shields.io/badge/PHP-7.2.0-brightgreen.svg) ![](https://img.shields.io/badge/Ubuntu-xenial-brightgreen.svg) ![](https://img.shields.io/docker/stars/xutl/php.svg) ![](https://img.shields.io/docker/pulls/xutl/php.svg)

## Container layout

Container                               | Distribution name        | PHP Version
--------------------------------------- | ------------------------ | --------------
`xutl/php:5.6`      | xenial (LTS)             | PHP 5.6
`xutl/php:7.0`      | xenial (LTS)             | PHP 7.0
`xutl/php:7.1`      | xenial (LTS)             | PHP 7.1
`xutl/php:7.2`      | xenial (LTS)             | PHP 7.2

## Filesystem layout

Directory                       | Description
------------------------------- | ------------------------------------------------------------------------------
`/usr/local/etc/php-fpm.d`       | php-fpm pool configuration
`/usr/local/etc/nginx`           | Nginx configuration path
`/usr/local/etc/nginx/sites`     | Nginx sites configuration path

File                                                | Description
--------------------------------------------------- | ------------------------------------------------------------------------------
`/usr/local/etc/php.ini`                          | PHP configuration
`/usr/local/etc/nginx/nginx.conf`                 | Global nginx configuration options
`/usr/local/etc/php-fpm.d/www.conf`               | php-fpm pool configuration
`/usr/local/etc/php/fpm/php-fpm.conf`             | PHP FPM daemon configuration

## Expand

Expand                                                | Version
--------------------------------------------------- | ------------------------------------------------------------------------------
`msgpack`                          | 0.5.7/2.0.2
`igbinary`                 | 2.0.5
`memcached`               | 2.2.0/3.0.4
`redis`             | 3.1.6
`yaml`             | 1.3.1/2.0.2
`xdebug`             | 2.5.5 Only build tags

## Build Tag

openssh-client git subversion curl wget nano unzip diffutils ntp openjdk-8-jdk yuicompressor closure-compiler nodejs-8.9.4 yarn-1.3.2

### Path
/usr/local/bin/yuicompressor.jar
/usr/local/bin/closure-compiler.jar


Expand                                                | Version
--------------------------------------------------- | ------------------------------------------------------------------------------
`msgpack`                          | 0.5.7/2.0.2
`igbinary`                 | 2.0.5
`memcached`               | 2.2.0/3.0.4
`redis`             | 3.1.6
`yaml`             | 1.3.1/2.0.2
`xdebug`             | 2.5.5 Only build tags

```shell
docker build --rm --build-arg PHP_VERSION=5.6.32 .
docker build --rm --build-arg PHP_VERSION=7.0.25 .
docker build --rm --build-arg PHP_VERSION=7.1.12 .
docker build --rm --build-arg PHP_VERSION=7.2.0 .
```