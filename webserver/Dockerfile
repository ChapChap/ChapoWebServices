FROM php:7-apache

RUN apt-get update -y && apt-get install -y libpng-dev

RUN docker-php-ext-install zip gd

RUN a2enmod rewrite
RUN service apache2 restart
