FROM php:7.1-fpm-alpine

# add phpredis to php-available-exts (install in the next step)
ENV PHPREDIS_VERSION 5.3.4
RUN mkdir -p /usr/src/php/ext/redis \
	&& curl -L https://github.com/phpredis/phpredis/archive/$PHPREDIS_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
	&& echo 'redis' >> /usr/src/php-available-ext

# update mirror url base on location ( Global or CN )
RUN sed -i "/dl-cdn.alpinelinux.org/s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" /etc/apk/repositories \
  && cat /etc/apk/repositories

# Extensions
RUN echo "Extensions install - start" \
    # Sockets
    && docker-php-ext-install sockets \
    # Amqp
    && apk add --no-cache --update rabbitmq-c-dev \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && pecl install -o -f amqp \
    && docker-php-ext-enable amqp \
    && apk del .phpize-deps \
    #
    && echo "Extensions install - finish"

# install required php extensions
RUN apk add --no-cache libzip-dev \
    && docker-php-ext-install pdo pdo_mysql opcache zip bcmath redis sockets  \
    && rm -rf /var/cache/apk/* /tmp/* \
    && docker-php-source delete

## install mcrypt
RUN apk add --no-cache  libmcrypt-dev libltdl \
    && docker-php-ext-configure mcrypt \
    && docker-php-ext-install mcrypt

## gd extension
RUN apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev && \
  docker-php-ext-configure gd \
    --with-gd \
    --with-freetype-dir=/usr/include/ \
    --with-png-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ && \
  NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
  docker-php-ext-install -j${NPROC} gd && \
  apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev

## Install intl
RUN apk add --no-cache icu-dev \
    && docker-php-ext-configure intl  \
    && docker-php-ext-install intl

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
  echo 'opcache.memory_consumption=128'; \
  echo 'opcache.interned_strings_buffer=8'; \
  echo 'opcache.max_accelerated_files=4000'; \
  echo 'opcache.revalidate_freq=2'; \
  echo 'opcache.fast_shutdown=1'; \
  echo 'opcache.enable_cli=1'; \
  } > /usr/local/etc/php/conf.d/opcache-recommended.ini
