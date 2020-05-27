# Base image
FROM php:7.3-apache

LABEL maintainer="info@tantn.com"

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# Run apt update and install some dependancies needed for docker-php-ext
RUN dpkg --add-architecture i386

# Update
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils

# OS lib
RUN apt-get -y --no-install-recommends install \
  procps libz-dev libmemcached-dev libpng-dev zlib1g-dev libcurl4-gnutls-dev libicu-dev \
  libmcrypt-dev libzip-dev libssl-dev dialog libsqlite3-dev libsqlite3-0 libxml2-dev
RUN apt-get -y --no-install-recommends install --fix-missing build-essential \
  git curl libcurl4 libcurl3-dev zip openssl

# Install Composer
RUN rm -rf /var/lib/apt/lists/* && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install xdebug
RUN pecl install xdebug-2.9.5 && docker-php-ext-enable xdebug

# Install redis
RUN pecl install redis-5.1.1 && docker-php-ext-enable redis

# Install bzip2
RUN apt-get update
RUN apt-get install -y libbz2-dev && docker-php-ext-install bz2

# Install memcached
RUN pecl install memcached
RUN echo extension=memcached.so >> /usr/local/etc/php/conf.d/memcached.ini

# Install mcrypt
RUN pecl install mcrypt-1.0.3 && docker-php-ext-enable mcrypt
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli

# PHP extensions
RUN docker-php-ext-install bcmath ctype fileinfo pdo tokenizer intl xml bcmath gd mysqli gettext
RUN docker-php-ext-install curl hash zip dom session mbstring xmlrpc pdo_mysql opcache json

# Install Freetype
RUN apt-get -y update && \
    apt-get --no-install-recommends install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install -j$(nproc) gd

# Tools
RUN apt-get install -y iputils-ping wget nano

# Cleanup
RUN apt-get clean
RUN rm -rf /usr/src/*
RUN rm -rf /var/lib/apt/lists/*

# Config Apache R+W
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data

# Enable mod_rewrite
RUN a2enmod rewrite

# RUN a2ensite default-ssl
RUN a2enmod ssl

# Add servername for apache
RUN printf "\nServerName localhost\n$(ls)" >> /etc/apache2/apache2.conf