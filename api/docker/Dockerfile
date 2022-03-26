FROM ubuntu:20.04

LABEL maintainer="Chris Fidao"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y gnupg tzdata \
    && echo "UTC" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

RUN apt-get update \
    && apt-get install -y --no-install-recommends gnupg \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu focal main" > /etc/apt/sources.list.d/ondrej-php.list \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4F4EA0AAE5267A6C \
    && apt-get update \
    && apt-get install -y curl zip unzip git supervisor sqlite3 \
    nginx php8.1-fpm php8.1-cli \
    php8.1-pgsql php8.1-sqlite3 php8.1-gd \
    php8.1-curl php8.1-memcached \
    php8.1-imap php8.1-mysql php8.1-mbstring \
    php8.1-xml php8.1-zip php8.1-bcmath php8.1-soap \
    php8.1-intl php8.1-readline php8.1-xdebug \
    php-msgpack php-igbinary \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    # && mkdir /run/php \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && echo "daemon off;" >> /etc/nginx/nginx.conf

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

ADD ./api/docker/default /etc/nginx/sites-available/default
ADD ./api/docker/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD ./api/docker/php-fpm.conf /etc/php/8.1/fpm/php-fpm.conf
ADD ./api/docker/www.conf /etc/php/8.1/fpm/pool.d/www.conf
ADD ./api/docker/start-container /usr/bin/start-container

COPY api /var/www/html
RUN cd /var/www/html && COMPOSER_ALLOW_SUPERUSER=1 /usr/bin/composer install --no-dev

RUN chmod +x /usr/bin/start-container

ENTRYPOINT ["start-container"]
