ARG PHP_VERSION

FROM wordpress:cli-php${PHP_VERSION:-8.1}

# Switch to root user to ensure we have the necessary permissions
USER root

RUN apk add --update linux-headers

# Install Xdebug
RUN apk --no-cache add pcre-dev ${PHPIZE_DEPS} \
  && pecl install xdebug \
  && docker-php-ext-enable xdebug \
  && apk del pcre-dev ${PHPIZE_DEPS}

# Configure Xdebug
RUN { \
    echo 'xdebug.mode=debug'; \
    echo 'xdebug.start_with_request=yes'; \
    echo 'xdebug.client_host=host.docker.internal'; \
    echo 'xdebug.client_port=9003'; \
    echo 'xdebug.log=/tmp/xdebug.log'; \
} > /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Ensure the configuration is loaded
RUN echo 'zend_extension=xdebug.so' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Switch back to the default user (if necessary)
USER www-data
