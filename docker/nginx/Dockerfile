FROM richarvey/nginx-php-fpm:latest
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
# Configure nginx
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
ADD conf/nginx-site.conf /etc/nginx/sites-available/default.conf
ADD conf/nginx-site-ssl.conf /etc/nginx/sites-available/default-ssl.conf

#####################################
# Set Memcached
#####################################
RUN sed -i 's/http\:\/\/dl-cdn.alpinelinux.org/https\:\/\/alpine.global.ssl.fastly.net/g' /etc/apk/repositories
ENV MEMCACHED_DEPS zlib-dev libmemcached-dev cyrus-sasl-dev git
RUN set -xe \
    && apk add --no-cache libmemcached-libs zlib \
    && apk add --no-cache \
        --virtual .memcached-deps \
        $MEMCACHED_DEPS \
    && git clone -b master https://github.com/php-memcached-dev/php-memcached /usr/src/php/ext/memcached \
    && docker-php-ext-configure /usr/src/php/ext/memcached \
        --disable-memcached-sasl \
    && docker-php-ext-install /usr/src/php/ext/memcached \
    && rm -rf /usr/src/php/ext/memcached \
    && apk del .memcached-deps
#####################################
# Set MongoDb
#####################################
RUN apk add --update  $PHPIZE_DEPS \
    && pecl install mongodb \
    && docker-php-ext-enable mongodb
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
# Configure Supervisord
#-#-#-#-#-#-#-#-#-#-#-#-#-#-#
#RUN apk update && apk add --no-cache supervisor
RUN mkdir -p /var/log/supervisor
ADD conf/supervisord.conf /etc/supervisord.conf
RUN supervisorctl reread \ supervisorctl update \ supervisorctl start laravel-worker:*
#####################################
# Add docker user
#####################################
RUN addgroup -S docker
RUN adduser -G docker -g "" -s /bin/sh -D docker
RUN echo "docker ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
