FROM hyperf/hyperf:7.3-alpine-v3.9-base

LABEL maintainer="Hyperf Developers <group@hyperf.io>" version="1.0" license="MIT"

RUN sed -i "s/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g" /etc/apk/repositories
##
# ---------- env settings ----------
##
ENV SWOOLE_VERSION=4.4.16 \
    #  install and remove building packages
    PHPIZE_DEPS="autoconf dpkg-dev dpkg file g++ gcc libc-dev make php7-dev php7-pear pkgconf re2c pcre-dev zlib-dev"

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 更新安装依赖包和PHP核心拓展
RUN set -ex \
        && apk update \
        # libs for swoole extension. libaio linux-headers
        && apk add --no-cache libstdc++ openssl \
        && apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libaio-dev libressl-dev rabbitmq-c rabbitmq-c-dev libcurl
# 安装 PECL 拓展，这里我们安装的是Redis
RUN pecl install redis && echo "extension=redis.so" > /etc/php7/conf.d/redis.ini
RUN pecl install amqp && echo "extension=amqp.so" > /etc/php7/conf.d/amqp.ini
#RUN pecl install imagick && echo "extension=imagick.so" > /usr/local/etc/php/conf.d/imagick.ini
RUN pecl install inotify && echo "extension=inotify.so" > /etc/php7/conf.d/inotify.ini
RUN pecl install msgpack && echo "extension=msgpack.so" > /etc/php7/conf.d/msgpack.ini
# 安装 Swoole
RUN cd /home \
    && git clone https://gitee.com/swoole/swoole.git \
    && cd swoole \
    && phpize \
    && ./configure --enable-openssl --enable-sockets --enable-mysqld \
    && make \
    && make install \
    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/swoole.ini \
    && echo "swoole.use_shortname = 'Off'" > /usr/local/etc/php/conf.d/swoole.ini \
    && php -v \
    && php -m \
    && php --ri swoole
# Composer
RUN   curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer \
  && composer self-update --clean-backups
# ---------- clear works ----------
RUN apk del .build-deps \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/local/bin/php* \
    && echo -e "\033[42;37m Build Completed :).\033[0m\n"
WORKDIR /data
# 写权限
RUN usermod -u 1000 www-data
