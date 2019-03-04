# Основываемся на контейнере версии 7.2-fpm
FROM php:7.2-fpm

# Устанавливаем необходимые для расширений пакеты
RUN apt-get update && apt-get install -y zlib1g-dev libicu-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libmagickwand-dev \
        libmagickcore-dev \
        libpng-dev \
        libxslt1-dev \
        zip unzip \
        --no-install-recommends

# Устанавливаем расширения intl, PDO MySQL, bcmath, xsl, zip, mysqli, soap
RUN pecl channel-update pecl.php.net \
    && docker-php-ext-install intl pdo_mysql bcmath xsl zip mysqli soap \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd \
    && docker-php-ext-install pcntl && docker-php-ext-enable pcntl \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug

# Устанавливаем vim и git
RUN apt-get update && apt-get install -y \
    build-essential \
    vim \
    git-core

# Удаляем ненужные пакеты и чистим образ
RUN apt-get purge -y g++ \
    && apt-get autoremove -y \
    && rm -r /var/lib/apt/lists/* \
    && rm -rf /tmp/*

# удаляем папку с конфигами по умолчанию для php-fpm
RUN rm -rf /usr/local/etc/php-fpm.d
# копируем конфиги из папки с проектов в контейнер
COPY ./pools /usr/local/etc/php-fpm.d
COPY ./php.ini /usr/local/etc/php/
COPY ./php-fpm.conf /usr/local/etc/php-fpm.conf
COPY ./docker-php-entrypoint /usr/local/bin/

# устанавливаем composer в контейнер
RUN curl -sS https://getcomposer.org/installer | php -- --filename=composer --install-dir=/usr/bin

ENTRYPOINT ["docker-php-entrypoint"]
CMD ["php-fpm"]