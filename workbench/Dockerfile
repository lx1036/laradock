#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#
# To edit the 'workspace' base Image, visit its repository on Github
#    https://github.com/lx1036/workspace
#
# To change its version, see the available Tags on the Docker Hub:
#    https://hub.docker.com/r/lx1036/workspace/tags/
#
# Note: Base Image name format {image-tag}-{php-version}
#
#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

FROM phusion/baseimage:latest

MAINTAINER Sunny Liu <xiang.liu@rightcapital.com>

RUN DEBIAN_FRONTEND=noninteractive
RUN locale-gen en_US.UTF-8

ENV LANGUAGE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV LC_CTYPE=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV TERM xterm

# Add the "PHP 7" ppa
RUN apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:ondrej/php

#
#--------------------------------------------------------------------------
# Software's Installation
#--------------------------------------------------------------------------
#

# Install "PHP Extentions", "libraries", "Software's"
RUN apt-get update && \
    apt-get install -y --allow-downgrades --allow-remove-essential \
        --allow-change-held-packages \
        php7.2-bcmath \
        php7.2-cli \
        php7.2-common \
        php7.2-curl \
        php7.2-dev \
        php7.2-gd \
        php7.2-geoip \
        php7.2-imagick \
        php7.2-intl \
        php7.2-json \
        php7.2-mbstring \
        php7.2-memcached \
        php7.2-mysql \
        php7.2-opcache \
        php7.2-pgsql \
        php7.2-redis \
        php7.2-soap \
        php7.2-sqlite \
        php7.2-sqlite3 \
        php7.2-xml \
        php7.2-yaml \
        php7.2-zip \
        pkg-config \
        libcurl4-openssl-dev \
        libedit-dev \
        libssl-dev \
        libxml2-dev \
        xz-utils \
        libsqlite3-dev \
        sqlite3 \
        git \
        curl \
        vim \
        nano \
        postgresql-client \
    && apt-get clean

#####################################
# Composer:
#####################################
# Install composer and add its bin to the PATH.
RUN curl -s http://getcomposer.org/installer | php && \
    echo "export PATH=${PATH}:/var/www/vendor/bin" >> ~/.bashrc && \
    mv composer.phar /usr/local/bin/composer

# Source the bash
RUN . ~/.bashrc

# Remove Faillog and Lastlog to reduce the size of the final image.
RUN rm /var/log/lastlog /var/log/faillog

#####################################
# Non-Root User:
#####################################
# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ARG PGID=1000

ENV PUID ${PUID}
ENV PGID ${PGID}

RUN groupadd -g ${PGID} lx1036 && \
    useradd -u ${PUID} -g lx1036 -m lx1036

#####################################
# Set Timezone
#####################################

ARG TZ=Asia/Chongqing
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

#####################################
# Composer:
#####################################
# Add the composer.json
COPY ./composer/composer.json /home/lx1036/.composer/composer.json
COPY ./composer/config.json /home/lx1036/.composer/config.json
# Make sure that ~/.composer belongs to lx1036
RUN chown -R lx1036:lx1036 /home/lx1036/.composer
USER lx1036

#####################################
# Crontab
#####################################
USER root
COPY ./crontab /etc/cron.d
RUN chmod -R 644 /etc/cron.d

#####################################
# User Aliases
#####################################
USER lx1036
COPY ./aliases.sh /home/lx1036/aliases.sh
RUN echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source /home/lx1036/aliases.sh" >> ~/.bashrc && \
	echo "" >> ~/.bashrc && \
	sed -i 's/\r//' /home/lx1036/aliases.sh && \
	sed -i 's/^#! \/bin\/sh/#! \/bin\/bash/' /home/lx1036/aliases.sh

USER root
RUN echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source /home/lx1036/aliases.sh" >> ~/.bashrc && \
	echo "" >> ~/.bashrc && \
	sed -i 's/\r//' /home/lx1036/aliases.sh && \
	sed -i 's/^#! \/bin\/sh/#! \/bin\/bash/' /home/lx1036/aliases.sh

#####################################
# xDebug:
#####################################
ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    # Load the xdebug extension only with phpunit commands
    apt-get update && \
    apt-get install -y --force-yes php7.2-xdebug && \
    sed -i 's/^;//g' /etc/php/7.2/cli/conf.d/20-xdebug.ini && \
    echo "alias phpunit='php -dzend_extension=xdebug.so /var/www/vendor/bin/phpunit'" >> ~/.bashrc \
;fi
# ADD for REMOTE debugging
COPY ./xdebug.ini /etc/php/7.2/cli/conf.d/xdebug.ini

#####################################
# ssh:
#####################################
ARG INSTALL_WORKSPACE_SSH=false
ENV INSTALL_WORKSPACE_SSH ${INSTALL_WORKSPACE_SSH}
ADD insecure_id_rsa /tmp/id_rsa
ADD insecure_id_rsa.pub /tmp/id_rsa.pub
RUN if [ ${INSTALL_WORKSPACE_SSH} = true ]; then \
    rm -f /etc/service/sshd/down && \
    cat /tmp/id_rsa.pub >> /root/.ssh/authorized_keys \
        && cat /tmp/id_rsa.pub >> /root/.ssh/id_rsa.pub \
        && cat /tmp/id_rsa >> /root/.ssh/id_rsa \
        && rm -f /tmp/id_rsa* \
        && chmod 644 /root/.ssh/authorized_keys /root/.ssh/id_rsa.pub \
    && chmod 400 /root/.ssh/id_rsa \
    && cp -rf /root/.ssh /home/lx1036 \
    && chown -R lx1036:lx1036 /home/lx1036/.ssh \
;fi

#####################################
# Swoole EXTENSION FOR PHP 7
#####################################
ARG INSTALL_SWOOLE=false
RUN if [ ${INSTALL_SWOOLE} = true ]; then \
    # Install Php Swoole Extension
    pecl -q install swoole && \
    echo "extension=swoole.so" >> /etc/php/7.2/mods-available/swoole.ini && \
    ln -s /etc/php/7.2/mods-available/swoole.ini /etc/php/7.2/cli/conf.d/20-swoole.ini \
;fi

#####################################
# Node / NVM:
#####################################
USER lx1036
# Check if NVM needs to be installed
ARG NODE_VERSION=stable
ENV NODE_VERSION ${NODE_VERSION}
ARG INSTALL_NODE=true
ENV INSTALL_NODE ${INSTALL_NODE}
ENV NVM_DIR /home/lx1036/.nvm
RUN if [ ${INSTALL_NODE} = true ]; then \
    # Install nvm (A Node Version Manager)
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash && \
        . $NVM_DIR/nvm.sh && \
        nvm install ${NODE_VERSION} && \
        nvm use ${NODE_VERSION} && \
        nvm alias ${NODE_VERSION} && \
        npm install -g gulp bower vue-cli \
;fi

# Wouldn't execute when added to the RUN statement in the above block
# Source NVM when loading bash since ~/.profile isn't loaded on non-login shell
RUN if [ ${INSTALL_NODE} = true ]; then \
    echo "" >> ~/.bashrc && \
    echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc \
;fi

# Add NVM binaries to root's .bashrc
USER root
RUN if [ ${INSTALL_NODE} = true ]; then \
    echo "" >> ~/.bashrc && \
    echo 'export NVM_DIR="/home/lx1036/.nvm"' >> ~/.bashrc && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> ~/.bashrc \
;fi
# Add PATH for node
ENV PATH $PATH:$NVM_DIR/versions/node/v${NODE_VERSION}/bin

#####################################
# YARN:
#####################################
USER lx1036
ARG INSTALL_YARN=true
ENV INSTALL_YARN ${INSTALL_YARN}
ARG YARN_VERSION=latest
ENV YARN_VERSION ${YARN_VERSION}
RUN if [ ${INSTALL_YARN} = true ]; then \
    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" && \
    if [ ${YARN_VERSION} = "latest" ]; then \
        curl -o- -L https://yarnpkg.com/install.sh | bash; \
    else \
        curl -o- -L https://yarnpkg.com/install.sh | bash -s -- --version ${YARN_VERSION}; \
    fi && \
    echo "" >> ~/.bashrc && \
    echo 'export PATH="$HOME/.yarn/bin:$PATH"' >> ~/.bashrc \
;fi

# Add YARN binaries to root's .bashrc
USER root
RUN if [ ${INSTALL_YARN} = true ]; then \
    echo "" >> ~/.bashrc && \
    echo 'export YARN_DIR="/home/lx1036/.yarn"' >> ~/.bashrc && \
    echo 'export PATH="$YARN_DIR/bin:$PATH"' >> ~/.bashrc \
;fi

#####################################
# Non-root user : PHPUnit path
#####################################
# add ./vendor/bin to non-root user's bashrc (needed for phpunit)
USER lx1036
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="/var/www/vendor/bin:$PATH"' >> ~/.bashrc

#####################################
# Deployer:
#####################################
USER lx1036

ARG INSTALL_DEPLOYER=false
ENV INSTALL_DEPLOYER ${INSTALL_DEPLOYER}

RUN if [ ${INSTALL_DEPLOYER} = true ]; then \
    # Install the Deployer
    composer global require "deployer/deployer" \
;fi

#####################################
# Linuxbrew:
#####################################
USER root
ARG INSTALL_LINUXBREW=false
ENV INSTALL_LINUXBREW ${INSTALL_LINUXBREW}
RUN if [ ${INSTALL_LINUXBREW} = true ]; then \
    # Preparation
    apt-get upgrade -y && \
    apt-get install -y build-essential make cmake scons curl git \
      ruby autoconf automake autoconf-archive \
      gettext libtool flex bison \
      libbz2-dev libcurl4-openssl-dev \
      libexpat-dev libncurses-dev && \

    # Install the Linuxbrew
    git clone --depth=1 https://github.com/Homebrew/linuxbrew.git ~/.linuxbrew && \

    echo "" >> ~/.bashrc && \
    echo 'export PKG_CONFIG_PATH"=/usr/local/lib/pkgconfig:/usr/local/lib64/pkgconfig:/usr/lib64/pkgconfig:/usr/lib/pkgconfig:/usr/lib/x86_64-linux-gnu/pkgconfig:/usr/lib64/pkgconfig:/usr/share/pkgconfig:$PKG_CONFIG_PATH"' >> ~/.bashrc && \

    # Setup linuxbrew
    echo 'export LINUXBREWHOME="$HOME/.linuxbrew"' >> ~/.bashrc && \
    echo 'export PATH="$LINUXBREWHOME/bin:$PATH"' >> ~/.bashrc && \
    echo 'export MANPATH="$LINUXBREWHOME/man:$MANPATH"' >> ~/.bashrc && \
    echo 'export PKG_CONFIG_PATH="$LINUXBREWHOME/lib64/pkgconfig:$LINUXBREWHOME/lib/pkgconfig:$PKG_CONFIG_PATH"' >> ~/.bashrc && \
    echo 'export LD_LIBRARY_PATH="$LINUXBREWHOME/lib64:$LINUXBREWHOME/lib:$LD_LIBRARY_PATH"' >> ~/.bashrc \
;fi

#####################################
# PYTHON:
#####################################
ARG INSTALL_PYTHON=true
ENV INSTALL_PYTHON ${INSTALL_PYTHON}
RUN if [ ${INSTALL_PYTHON} = true ]; then \
  apt-get update \
  && apt-get -y install python python-pip python-dev build-essential  \
  && pip install --upgrade pip  \
  && pip install --upgrade virtualenv \
;fi

#
#--------------------------------------------------------------------------
# Final Touch
#--------------------------------------------------------------------------
#
# Clean up
USER root
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Set default work directory
WORKDIR /var/www
