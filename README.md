# Laravel con las extensiones de ldap y sqlserver

FROM php:7.4-apache

RUN apt-get update && \
    apt-get install -y libldap2-dev && \
    apt-get install -y libpq-dev && \
    rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install ldap

# Instala los controladores de SQL Server compatibles con PHP 7.4
RUN apt-get update && \
    apt-get install -y unixodbc-dev && \
    pecl install sqlsrv-5.9.0 pdo_sqlsrv-5.9.0 && \
    docker-php-ext-enable sqlsrv pdo_sqlsrv

# Requisitos de Laravel y PhpSpreadsheet
RUN apt-get update && \
    apt-get install -y libzip-dev zip unzip libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install -j$(nproc) gd zip

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

RUN a2enmod rewrite

WORKDIR /var/www/public
