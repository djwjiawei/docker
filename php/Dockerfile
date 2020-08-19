FROM php:7.3-fpm-alpine

ARG test

# Version
ENV PHPREDIS_VERSION 4.0.0

ENV SWOOLE_VERSION 4.4.17

# Libs
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk  add  \
        curl \
        vim  \
        wget \
        git \
        openssl-dev\
        zip \
        unzip \
        g++  make autoconf \
        libevent-dev \
        libtool \
        busybox-extras

# Composer
RUN wget https://mirrors.aliyun.com/composer/composer.phar \
    && chmod 755 composer.phar \
    && mv composer.phar /usr/local/bin/composer \
    && composer self-update --clean-backups

# docker方式安装PDO extension                                                                                # 安装扩展
RUN   mv "$PHP_INI_DIR/php.ini-production"  "$PHP_INI_DIR/php.ini" &&  docker-php-ext-install pdo_mysql &&   docker-php-ext-install pcntl && docker-php-ext-install sysvmsg

# Redis extension
RUN wget http://pecl.php.net/get/redis-${PHPREDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
    && pecl install /tmp/redis.tar.tgz \
    && rm -rf /tmp/redis.tar.tgz \
    && docker-php-ext-enable redis
 # 修改php.ini的文件 extension=redis.so

 # swoole ext
RUN wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -O swoole.tar.gz \
     && mkdir -p swoole \
     && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
     && rm swoole.tar.gz \
     && ( \
     cd swoole \
     && phpize \
     && ./configure --enable-openssl \
     && make \
     && make install \
     )



#开启扩展
RUN docker-php-ext-enable swoole

RUN docker-php-ext-install sockets

RUN pecl install event

RUN docker-php-ext-enable --ini-name docker-php-zz-event.ini event

#切换镜像
RUN  composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/ && mkdir /www && chown www-data /www

#设置工作目录
WORKDIR  /www





