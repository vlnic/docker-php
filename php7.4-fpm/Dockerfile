FROM php:7.4-fpm-alpine

MAINTAINER Pavlov Vladimir p4vlow.vl4d@yandex.ru

ENV TZ=Europe/Moscow
RUN rm -rf /etc/localtime && \
    ln -s /usr/share/zoneinfo/Europe/Moscow /etc/localtime

RUN apk update \
    && apk upgrade \
    && apk add --no-cache bash git vim curl wget libzip libzip-dev openssl libressl-dev musl-dev libffi-dev

RUN set -ex \
  && apk --no-cache add --virtual .build-deps $PHPIZE_DEPS \
  && docker-php-ext-install \
        pdo pdo_mysql \
        bcmath \
        tokenizer zip sockets \
        opcache \
  && docker-php-source extract \
  && pecl install \
        xdebug-beta \
        apcu \
  && docker-php-ext-enable \
        xdebug \
        opcache \
        apcu \
  && apk add --update icu-dev \
  && apk add --update \
        libevent-dev \
        openssl-dev\
  && pecl install event \
  && docker-php-ext-enable event \
  && mv /usr/local/etc/php/conf.d/docker-php-ext-event.ini \
        /usr/local/etc/php/conf.d/docker-php-zz-event.ini \
  && apk add --no-cache freetype libpng libjpeg-turbo freetype-dev libpng-dev libjpeg-turbo-dev \
    && docker-php-ext-configure gd \
        --with-freetype \
        --with-jpeg \
    &&  NPROC=$(grep -c ^processor /proc/cpuinfo 2> /dev/null || 1)  \
    && docker-php-ext-install -j$(nproc) gd \
    && apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev \
  && apk del --no-cache \
        autoconf \
        binutils \
        gcc \
        g++ \
        m4 \
        make \
        musl-dev \
        libressl-dev \
        pkgconf \
        pkgconfig \
        perl \
        libzip-dev \
        libtool \
        libffi-dev \
   && rm /var/cache/apk/*

# install composer globally
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && rm -rf /tmp/* /var/tmp/* /usr/share/doc/*

RUN mkdir /home/app/ && \
    apk --no-cache add shadow && \
    groupadd -g 1000 app && \
    useradd -g 1000 -u 1000 -b /home/app app && \
    chown app:app -R /home/*

USER app

WORKDIR /var/www

