# Set master image
ARG LARADOCK_PHP_VERSION
FROM php:${LARADOCK_PHP_VERSION}-fpm-buster

# Copy composer.lock and composer.json
#COPY composer.lock composer.json /var/www/

# Install Additional dependencies
RUN apt-get update && apt-get install -y locales \
    libmagickwand-dev \
    libpng-dev \
    libwebp-dev \
    libjpeg-dev \
    libpng-dev libxpm-dev \
    libfreetype6-dev \
    libicu-dev \
    libjpeg62-turbo-dev \
    libzip-dev \
    zlib1g-dev \
    libonig-dev \
    g++ \
    unzip \
    librdkafka-dev \
    libmcrypt-dev \
    gettext  \
    npm \
    openssl \
    zip --no-install-recommends

# Install dependences for crawler
RUN apt-get update && apt-get install -y \
    libnss3 \
    libnspr4 \
    libatk1.0-0 \
    libatk-bridge2.0-0 \
    libcups2 \
    libdrm2 \
    libxkbcommon0 \
    libxcomposite1 \
    libxdamage1 \
    libxfixes3 \
    libxrandr2 \
    libgbm1 \
    libasound2

RUN apt-get update && apt-get install -y rsync

ARG LARADOCK_PHP_VERSION
RUN if [ ${LARADOCK_PHP_VERSION} = "7.2" ] || [ ${LARADOCK_PHP_VERSION} = "7.4" ]; then \
        pecl install -o -f redis imagick rdkafka mcrypt mongodb \
&& rm -rf /tmp/pear; \
      else \
        pecl install -o -f redis xdebug imagick rdkafka mcrypt mongodb \
&& rm -rf /tmp/pear; \
      fi

# Install LDAP
RUN \
    apt-get update && \
    apt-get install libldap2-dev -y && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap

ARG LARADOCK_PHP_VERSION
RUN if [ ${LARADOCK_PHP_VERSION} = "7.2" ]; then \
        docker-php-ext-configure gd --with-freetype-dir --with-jpeg-dir --with-png-dir; \
      else \
        docker-php-ext-configure gd --with-freetype --with-jpeg; \
      fi

RUN docker-php-ext-configure pcntl --enable-pcntl
RUN docker-php-ext-install pdo pdo_mysql pcntl opcache gd zip bcmath mysqli mbstring exif gettext sockets

ARG LARADOCK_PHP_VERSION
RUN if [ ${LARADOCK_PHP_VERSION} = "7.2" ] || [ ${LARADOCK_PHP_VERSION} = "7.4" ]; then \
        docker-php-ext-enable pdo pdo_mysql redis imagick rdkafka mcrypt mongodb; \
      else \
        docker-php-ext-enable pdo pdo_mysql redis xdebug imagick rdkafka mcrypt mongodb; \
      fi

# Install PHP Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN curl -sL https://deb.nodesource.com/setup_16.x | bash -
RUN apt-get install -y nodejs wget
RUN npm install -g cross-env

# Configure non-root user.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupmod -o -g ${PGID} www-data && \
    usermod -o -u ${PUID} -g www-data www-data

# Set working directory
ARG APP_CODE_PATH_CONTAINER
ARG PHP_OPCACHE_VALIDATE_TIMESTAMPS

COPY xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
# COPY opcache.ini /usr/local/etc/php/conf.d/opcache.ini

# Copy existing application directory permissions
COPY --chown=www-data:www-data . ${APP_CODE_PATH_CONTAINER}

#change ownership of our applications
RUN chown -R www-data:www-data /var/www

WORKDIR ${APP_CODE_PATH_CONTAINER}

# Change current user to www
USER www-data
