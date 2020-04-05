ARG PHP_VERSION=7.3

FROM php:${PHP_VERSION}-fpm-alpine

LABEL maintainer="YangXingshun <250915790@qq.com>"

ARG PHP_VERSION=7.3

RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/' /etc/apk/repositories

RUN apk --update add tar libevent-dev


# Install sockets package:
ARG INSTALL_SOCKETS=true
RUN if [ ${INSTALL_SOCKETS} = true ]; then \
	docker-php-ext-install sockets \
	&& docker-php-ext-enable --ini-name 0-sockets.ini sockets \
;fi

#Install GD package:
ARG INSTALL_GD=true
RUN if [ ${INSTALL_GD} = true ]; then \
   apk add --update --no-cache libpng-dev; \
   docker-php-ext-install gd \
;fi

#Install GMP package:
ARG INSTALL_GMP=false
RUN if [ ${INSTALL_GMP} = true ]; then \
   apk add --update --no-cache gmp gmp-dev \
   && docker-php-ext-install gmp \
;fi

# Install Redis package:
ARG INSTALL_REDIS=true
RUN if [ ${INSTALL_REDIS} = true ]; then \
    # Install Redis Extension
    printf "\n" | pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis \
;fi


###########################################################################
# pcntl
###########################################################################

ARG INSTALL_PCNTL=true
RUN if [ ${INSTALL_PCNTL} = true ]; then \
    # Installs pcntl, helpful for running Horizon
    docker-php-ext-install pcntl \
;fi


###########################################################################
# event
###########################################################################

ARG INSTALL_EVENT=false
RUN if [ ${INSTALL_EVENT} = true ]; then \
	# 注意提示：Include libevent OpenSSL support [yes] : 时输入no回车
    # 注意提示：PHP Namespace for all Event classes :时输入yes，其它直接敲回车就行
    # 扩展 sockets 必须在 event前加载 ，可使用：docker-php-ext-enable --ini-name 0-sockets.ini sockets ，把加载顺序提前
	pecl install event \
;fi

###########################################################################
# Swoole EXTENSION
###########################################################################

ARG INSTALL_SWOOLE=false

RUN if [ ${INSTALL_SWOOLE} = true ]; then \
    # Install Php Swoole Extension
    if [ $(php -r "echo PHP_MAJOR_VERSION;") = "5" ]; then \
      pecl -q install swoole-2.0.10; \
    else \
      if [ $(php -r "echo PHP_MINOR_VERSION;") = "0" ]; then \
        pecl install swoole-2.2.0; \
      else \
        pecl install swoole; \
      fi \
    fi \
    && docker-php-ext-enable swoole \
;fi


#
#--------------------------------------------------------------------------
# Check PHP version
#--------------------------------------------------------------------------
#

RUN php -v | head -n 1 | grep -q "PHP ${PHP_VERSION}."


# Clean up
RUN rm /var/cache/apk/* \
    && mkdir -p /var/www

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000