FROM php:7.4-fpm
MAINTAINER goozp "gzp@goozp.com"

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN sed -i "s/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g" /etc/apk/repositories
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
    openssl \
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
    && apk del .build-deps \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/local/bin/php* \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"

# Composer
RUN   curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer \
  && composer self-update --clean-backups
WORKDIR /data

# 写权限
RUN usermod -u 1000 www-data
