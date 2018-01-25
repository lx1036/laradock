#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#
# To edit the 'php-fpm' base Image, visit its repository on Github
#    https://github.com/Laradock/php-fpm
#
# To change its version, see the available Tags on the Docker Hub:
#    https://hub.docker.com/r/laradock/php-fpm/tags/
#
# Note: Base Image name format {image-tag}-{php-version}
#

FROM ubuntu:xenial

MAINTAINER Sunny Liu <xiang.liu@rightcapital.com>

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#
# Installing tools and PHP extentions using "apt", "docker-php", "pecl",
#

RUN set -xe \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" >> /etc/apt/sources.list \
    && echo "deb-src http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" >> /etc/apt/sources.list \
    && apt-get update && apt-get install -y --allow-unauthenticated \
        php7.2-cli \
        php7.2-fpm \
        php7.2-mysql \
        php7.2-curl \
        php7.2-zip \
        php7.2-mbstring \
        php7.2-xml \
        php7.2-gd \
        php7.2-geoip \
        php7.2-soap \
        php7.2-yaml \
        php7.2-redis \
        php7.2-imagick \
        php7.2-opcache \
        --no-install-recommends \
        --no-install-suggests


#&& rm -rf /var/lib/apt/lists/*


#####################################
# Opcache:
#####################################
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini


#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------

ADD ./laravel.ini /usr/local/etc/php/conf.d
ADD ./xlaravel.pool.conf /usr/local/etc/php-fpm.d/

#RUN rm -r /var/lib/apt/lists/*

RUN usermod -u 1000 www-data

WORKDIR /var/www

CMD ["php-fpm"]

EXPOSE 9000
