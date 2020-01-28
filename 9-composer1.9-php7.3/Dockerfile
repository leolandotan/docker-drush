FROM llt104/composer:1.9-php7.3

# Add common extensions
RUN apk --update add \
    build-base \
    libpq \
    mysql-client \
    postgresql-client \
    postgresql-dev \
    php7-pear \
    patch \
    tar \
    autoconf \
    g++ \
    gcc \
    # yaml \
    # libyaml-dev \
    yaml-dev && \
    docker-php-ext-install mysqli pgsql pdo_mysql pdo_pgsql && \
    apk del build-base postgresql-dev && \
    rm -rf /var/cache/apk/*

# Add the Redis PHP module.
RUN git clone --branch="master" https://github.com/phpredis/phpredis.git /usr/src/php/ext/redis && \
  # Install the Redis module.
  docker-php-ext-install redis && \
  # Test to make sure it's available.
  php -m && php -r "new Redis();"

# Add in Memcached PHP module
ENV MEMCACHED_DEPS zlib-dev libmemcached-dev cyrus-sasl-dev
RUN apk add --no-cache --update libmemcached-libs zlib
RUN set -xe \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && apk add --no-cache --update --virtual .memcached-deps $MEMCACHED_DEPS \
    && pecl install memcached \
    && echo "extension=memcached.so" > /usr/local/etc/php/conf.d/20_memcached.ini \
    && rm -rf /usr/share/php7 \
    && rm -rf /tmp/* \
    && apk del .memcached-deps .phpize-deps \
    # Test to make sure it's available
    && php -m && php -r "new Memcached();"

# Update the entry point of the application
ENTRYPOINT ["drush"]

# Set the Drush version.
ENV DRUSH_VERSION 9.7.1

RUN composer global require consolidation/cgr && \
  echo 'export PATH="$(composer config -g home)/vendor/bin:$PATH"' >> ~/.bashrc

RUN ln -s /tmp/vendor/bin/cgr /usr/local/bin/cgr

RUN cgr drush/drush:"$DRUSH_VERSION" --prefer-dist

RUN ln -s /tmp/vendor/bin/drush /usr/local/bin/drush

RUN php -v

CMD ["drush"]
