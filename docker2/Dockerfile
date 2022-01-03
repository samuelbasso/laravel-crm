FROM php:8.0-apache

ARG USERNAME
ARG USER_UID
ARG USER_GID

RUN apt-get update && apt-get install -y --no-install-recommends sudo git mariadb-client \
    libfreetype6-dev libpng-dev libjpeg62-turbo-dev libxml2-dev zip unzip libzip-dev

RUN groupadd --gid $USER_GID $USERNAME && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME
RUN echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && chmod 0440 /etc/sudoers.d/$USERNAME

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j"$(nproc)" gd pdo_mysql iconv  soap zip intl opcache

RUN pecl install xdebug && docker-php-ext-enable xdebug
COPY docker2/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
COPY docker2/php.ini /usr/local/etc/php/conf.d/php.ini

ENV APACHE_DOCUMENT_ROOT=/var/www/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf \
    && sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf \
    && a2enmod rewrite

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN apt-get autoremove && apt-get clean && rm -rf /var/lib/apt/lists/*
