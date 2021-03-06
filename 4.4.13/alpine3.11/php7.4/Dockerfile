FROM cdce/php:7.4-ext as builder

ENV EXT_SWOOLE_VERSION 4.4.13
ENV EXT_DOWNLOAD_DIR /usr/src/ext

RUN set -ex \
    && docker-php-source extract \
    && mkdir -p $EXT_DOWNLOAD_DIR \
    && cd $EXT_DOWNLOAD_DIR \
    && wget -O swoole-src-$EXT_SWOOLE_VERSION.tar.gz https://github.com/swoole/swoole-src/archive/v$EXT_SWOOLE_VERSION.tar.gz \
    && tar xf swoole-src-$EXT_SWOOLE_VERSION.tar.gz \
    && mv swoole-src-$EXT_SWOOLE_VERSION /usr/src/php/ext/swoole \
    && apk add --no-cache openssl-dev \
    && docker-php-ext-configure swoole --enable-openssl \
    && docker-php-ext-install swoole 

FROM cdce/php:7.4-ext as prod

LABEL MAINTAINER "Zhao Weidong <zhaoweidong@300.cn>"

COPY --from=0 /usr/local/lib/php/extensions/no-debug-non-zts-20190902/swoole.so /usr/local/lib/php/extensions/no-debug-non-zts-20190902/
COPY docker-php-entrypoint /usr/local/bin/
RUN set -ex \
    && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk add --no-cache --virtual .rundeps \
               bash \
               su-exec; \
    \
     \
      \
    ## Remove the gid 300 group.
    delgroup abuild \
    && addgroup -g 300 -S zceo \
    && adduser -S -H -k -D -s /sbin/nologin -G zceo -u 300 zceo; \
    \
    docker-php-ext-enable swoole; \
    \
     \
      \
    cd /usr/local/etc/php \
    && curl -o php.ini https://raw.githubusercontent.com/1993Plus/docker-php/master/php.ini; \
    \
##### Modify docker-php-entrypoint,remove swoole server pid file.
     \
    cd /usr/bin \
    && ln -snf ../local/bin/docker-php-entrypoint docker-php-entrypoint \
    && mkdir -p /apps \
    && wget -O /apps/run.sh https://raw.githubusercontent.com/1993Plus/docker-php/master/run.sh \
    && chmod +x /apps/run.sh

WORKDIR /app

CMD ["php"]
