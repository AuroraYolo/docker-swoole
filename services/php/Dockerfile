FROM php:7.3-fpm
MAINTAINER goozp "gzp@goozp.com"

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# 更新安装依赖包和PHP核心拓展
RUN apt-get update && apt-get install -y \
        procps \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
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
        git && \
        apt autoremove && apt clean

# install php pdo_mysql opcache
# WARNING: Disable opcache-cli if you run you php
RUN docker-php-ext-configure gd  --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install \
    iconv \
    gd \
    pdo_mysql \
    bcmath \
    mysqli \
    iconv \
    mbstring \
    json \
    opcache \
    sockets \
    pcntl && \
    echo "opcache.enable_cli=1" >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini

# 将预先下载好的拓展包从宿主机拷贝进去
COPY ./pkg/redis.tgz /home/redis.tgz
#COPY ./pkg/cphalcon.tar.gz /home/cphalcon.tar.gz
COPY ./pkg/grpc.tgz /home/grpc.tgz
COPY ./pkg/amqp.tgz /home/amqp.tgz
#COPY ./pkg/composer.phar /home/composer.phar
#COPY ./pkg/imagick.tgz /home/imagick.tgz
COPY ./pkg/msgpack.tgz /home/msgpack.tgz
COPY ./pkg/inotify.tgz /home/inotify.tgz
#COPY ./pkg/zookeeper.tgz /home/zookeeper.tgz
COPY ./pkg/yar.tgz /home/yar.tgz
COPY ./pkg/grpc.tgz /home/grpc.tgz
#COPY ./pkg/libmemcached.tar.gz /home/libmemcached.tar.gz
#COPY ./pkg/memcached.tgz /home/memcached.tgz

# 安装 PECL 拓展，这里我们安装的是Redis
RUN pecl install /home/redis.tgz && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini
RUN pecl install /home/grpc.tgz && echo "extension=grpc.so" > /usr/local/etc/php/conf.d/grpc.ini
RUN pecl install /home/amqp.tgz && echo "extension=amqp.so" > /usr/local/etc/php/conf.d/amqp.ini
#RUN pecl install /home/imagick.tgz && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/imagick.ini
RUN pecl install /home/inotify.tgz && echo "extension=inotify.so" > /usr/local/etc/php/conf.d/inotify.ini
RUN pecl install /home/msgpack.tgz && echo "extension=msgpack.so" > /usr/local/etc/php/conf.d/msgpack.ini
#RUN pecl install /home/zookeeper.tgz && echo "extension=zookeeper.so" > /usr/local/etc/php/conf.d/zookeeper.ini
#RUN pecl install /home/yar.tgz && echo "extension=yar.so" > /usr/local/etc/php/conf.d/yar.ini
RUN pecl install /home/grpc.tgz && echo "extension=grpc.so" > /usr/local/etc/php/conf.d/grpc.ini
# 安装第三方拓展，这里是 Phalcon 拓展
#RUN cd /home \
   # && tar -zxvf cphalcon.tar.gz \
   # && mv cphalcon-* phalcon \
   # && cd phalcon/build \
   # && ./install \
   # && echo "extension=phalcon.so" > /usr/local/etc/php/conf.d/phalcon.ini
# 安装libmemcached
#RUN tar zxvf /home/libmemcached.tar.gz \
   # && cd libmemcached-1.0.18 \
   # && ./configure --prefix=/usr/local/libmemcached --with-memcached \
  #  && make && make install

# 安装memcached
#RUN tar zxvf /home/memcached.tgz \
   # && cd memcached-3.0.4 \
   # && phpize \
    #&& ./configure -enable-memcached -with-php-config=/usr/local/bin/php-config -with-zlib-dir -with-libmemcached-dir=/usr/local/libmemcached -prefix=/usr/local/phpmemcached -disable-memcached-sasl \
   # && make && make install \
    #&& echo "extension=memcached.so" > /usr/local/etc/php/conf.d/memcached.ini

# 安装 Swoole
RUN cd /home \
    && git clone https://gitee.com/swoole/swoole.git \
    && cd swoole \
    && phpize \
    && ./configure --enable-openssl --enable-sockets --enable-mysqld -with-php-config=/usr/local/bin/php-config \
    && make \
    && make install \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini


# 删除扩展包
RUN rm -f /home/redis.tgz
#RUN rm -f /home/libmemcached.tar.gz
#RUN rm -f /home/memcached.tgz
RUN rm -f /home/amqp.tgz
RUN rm -f /home/msgpack.tgz
RUN rm -f /home/inotify.tgz
RUN rm -f /home/imagick.tgz
RUN rm -f /home/grpc.tgz
#RUN rm -f /home/zookeeper.tgz
RUN rm -f /home/yar.tgz
RUN rm -f /home/grpc.tgz

# Composer
RUN   curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer \
  && composer self-update --clean-backups
WORKDIR /data

# 写权限
RUN usermod -u 1000 www-data
