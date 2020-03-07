FROM php:7.4-fpm
MAINTAINER goozp "gzp@goozp.com"

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        procps \
        libfreetype6-dev \
        libpng-dev \
        openssl \
        libssh-dev \
        librabbitmq-dev \
        libpcre3 \
        libpcre3-dev \
        libnghttp2-dev \
        libhiredis-dev \
        curl \
        wget \
        zip \
        zlibc \
        unzip \
        git \
        libicu-dev \
        libmpdec-dev \
        libpq-dev \
        libjpeg-dev \
        libzip-dev \
        supervisor \
        unzip \
        vim \
        apt autoremove && apt clean

# install php pdo_mysql opcache
# WARNING: Disable opcache-cli if you run you php
RUN docker-php-ext-install \
    iconv \
    gd \
    openssl \
    pdo_mysql \
    bcmath \
    mysqli \
    iconv \
    mbstring \
    json \
    opcache \
    sockets \
    pcntl \
    zip \
    echo "opcache.enable_cli=1" >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini

# 安装 PECL 拓展，这里我们安装的是Redis
RUN pecl install redis && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini
RUN pecl install grpc && echo "extension=grpc.so" > /usr/local/etc/php/conf.d/grpc.ini
RUN pecl install amqp && echo "extension=amqp.so" > /usr/local/etc/php/conf.d/amqp.ini
#RUN pecl install imagick && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/imagick.ini
RUN pecl install inotify && echo "extension=inotify.so" > /usr/local/etc/php/conf.d/inotify.ini
RUN pecl install msgpack && echo "extension=msgpack.so" > /usr/local/etc/php/conf.d/msgpack.ini
RUN pecl install zookeeper && echo "extension=zookeeper.so" > /usr/local/etc/php/conf.d/zookeeper.ini
RUN pecl install yar && echo "extension=yar.so" > /usr/local/etc/php/conf.d/yar.ini
RUN pecl install grpc && echo "extension=grpc.so" > /usr/local/etc/php/conf.d/grpc.ini
# 安装 Swoole
RUN cd /home \
    && git clone https://gitee.com/swoole/swoole.git \
    && cd swoole \
    && phpize \
    && ./configure --enable-openssl --enable-sockets --enable-mysqld -with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini \
    && echo "swoole.use_shortname = 'Off'" > /usr/local/etc/php/conf.d/swoole.ini \

# clear
    && php -v \
    && php -m \
    && php --ri swoole \
    # ---------- clear works ----------
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"

# Composer
RUN   curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer \
  && composer self-update --clean-backups
WORKDIR /data

# 写权限
RUN usermod -u 1000 www-data
