FROM ubuntu:16.04
RUN set -xe \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" >> /etc/apt/sources.list \
    && echo "deb-src http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y --allow-unauthenticated \
        php7.2-cli \
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
        --no-install-recommends \
        --no-install-suggests \
