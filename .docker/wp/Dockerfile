ARG PHP_VERSION

FROM wordpress:php${PHP_VERSION}-fpm-alpine

ARG PHP_MAX_INPUT_VARS
ARG PHP_MEMORY_LIMIT
ARG PHP_MAX_EXECUTION_TIME

RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
RUN sed -i "s/;max_input_vars = 1000/max_input_vars = ${PHP_MAX_INPUT_VARS}/" /usr/local/etc/php/php.ini
RUN sed -i "s/memory_limit = 128M/memory_limit = ${PHP_MEMORY_LIMIT}/" /usr/local/etc/php/php.ini
RUN sed -i "s/max_execution_time = 30/max_execution_time = ${PHP_MAX_EXECUTION_TIME}/" /usr/local/etc/php/php.ini

RUN apk add --update linux-headers

RUN apk --no-cache add pcre-dev ${PHPIZE_DEPS} \
  && pecl install xdebug \
  && docker-php-ext-enable xdebug \
  && apk del pcre-dev ${PHPIZE_DEPS}
