FROM php:7.0-apache

# Set Timezone / Date
RUN echo "Europe/Berlin" > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata

# Install packages
RUN apt-get update && \
	apt-get -y upgrade --fix-missing && \
	apt-get install -y --force-yes \
		less vim wget unzip rsync mysql-client git \
		jpegoptim optipng && \
	apt-get clean && \
	echo "export TERM=xterm" >> /root/.bashrc

# GD, with jpg
RUN apt-get update && apt-get install -y \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libmcrypt-dev \
		libpng12-dev \
	&& docker-php-ext-install -j$(nproc) iconv mcrypt \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd

# add Xdebug
RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini

# MySQL / DB connection
RUN docker-php-ext-install mysqli pdo pdo_mysql

RUN docker-php-ext-install bcmath

# SOAP
RUN apt-get install -y --force-yes libxml2-dev
RUN docker-php-ext-install soap


# Activate Modules
RUN a2enmod rewrite && \
    a2enmod headers && \
    a2enmod expires
