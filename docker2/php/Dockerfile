FROM php:7.3-fpm

ARG USERNAME
ARG USER_UID
ARG USER_GID

RUN apt-get update && apt-get install -y --no-install-recommends sudo git mariadb-client \
    libfreetype6-dev libpng-dev libjpeg62-turbo-dev libxml2-dev zip unzip libzip-dev firebird-dev

RUN groupadd --gid $USER_GID $USERNAME && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME
RUN echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && chmod 0440 /etc/sudoers.d/$USERNAME

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j"$(nproc)" pdo_mysql iconv gd soap zip pdo_firebird

RUN pecl install xdebug && docker-php-ext-enable xdebug
COPY docker2/php/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN apt-get autoremove && apt-get clean && rm -rf /var/lib/apt/lists/*
