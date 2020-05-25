# Base image
FROM php:7.3-apache

# Fix debconf warnings upon build
ARG DEBIAN_FRONTEND=noninteractive

# Run apt update and install some dependancies needed for docker-php-ext
RUN dpkg --add-architecture i386

RUN apt-get update && apt install -y \
  procps \
  sqlite3 \
  libicu-dev \
  zlib1g-dev \
  libz-dev \
  libmemcached-dev \
  libpng-dev \
  libxml2-dev \
  libmcrypt-dev \
  libcurl4-gnutls-dev \
  libzip-dev \ 
  openssl \ 
  libssl-dev

RUN apt-get install -y libbz2-dev && docker-php-ext-install bz2

RUN pecl install memcached
RUN echo extension=memcached.so >> /usr/local/etc/php/conf.d/memcached.ini

RUN pecl install mcrypt-1.0.2 && docker-php-ext-enable mcrypt
RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli

RUN docker-php-ext-install bcmath ctype fileinfo pdo tokenizer intl xml bcmath gd mysqli
RUN docker-php-ext-install curl hash zip dom session mbstring xmlrpc pdo_mysql opcache json

RUN apt-get clean

# Update web root to public
# See: https://hub.docker.com/_/php#changing-documentroot-or-other-apache-configuration
ENV APACHE_DOCUMENT_ROOT /var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

# Enable mod_rewrite
RUN a2enmod rewrite