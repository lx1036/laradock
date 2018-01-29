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

FROM workbench:latest

MAINTAINER Sunny Liu <xiang.liu@rightcapital.com>

# Add the "PHP 7" ppa
RUN apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:ondrej/php

# Install "PHP Extentions", "libraries", "Software's"
RUN apt-get update && \
    apt-get install -y --allow-downgrades --allow-remove-essential \
        --allow-change-held-packages \
        php7.2-fpm \
    && apt-get clean
#####################################
# Opcache:
#####################################
COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini

#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
COPY ./conf.d/ /usr/local/etc/php/conf.d/
COPY ./php-fpm.d /usr/local/etc/php-fpm.d/
ADD ./laravel.ini /usr/local/etc/php/conf.d/
ADD ./xlaravel.pool.conf /usr/local/etc/php-fpm.d/

#RUN rm -r /var/lib/apt/lists/*

#RUN usermod -u 1000 www-data

WORKDIR /var/www

#CMD ["/bin/sh", "-c", "php-fpm"]

EXPOSE 9000
