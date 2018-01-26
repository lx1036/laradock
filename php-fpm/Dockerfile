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

FROM php:7.1-fpm

MAINTAINER Sunny Liu <xiang.liu@rightcapital.com>

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
