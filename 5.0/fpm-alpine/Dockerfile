FROM php:8-fpm-alpine
LABEL maintainer="markus@martialblog.de"
ARG version='5.2.1+211111'
ARG sha256_checksum='0ea7386b8a0b2cb5a3e1cabbfffb383c1f690fbdd32d4b1de68f9ce36e5c7e2a'
ARG USER=www-data

# Install OS dependencies
RUN set -ex; \
        apk add --no-cache --virtual .build-deps \
        freetype-dev \
        libpng-dev \
        libzip-dev \
        libjpeg-turbo-dev \
        tidyhtml-dev \
        libsodium-dev \
        openldap-dev \
        oniguruma-dev \
        imap-dev \
        postgresql-dev && \
        apk add --no-cache netcat-openbsd bash

# Install PHP Plugins
RUN set -ex; \
        docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr && \
        docker-php-ext-configure imap --with-imap-ssl && \
        docker-php-ext-install \
        exif \
        gd \
        imap \
        ldap \
        mbstring \
        pdo \
        pdo_mysql \
        pdo_pgsql \
        pgsql \
        sodium \
        tidy \
        zip

# Download, unzip and chmod LimeSurvey from official GitHub repository
RUN set -ex; \
        curl -sSL "https://github.com/LimeSurvey/LimeSurvey/archive/${version}.tar.gz" --output /tmp/limesurvey.tar.gz && \
        echo "${sha256_checksum}  /tmp/limesurvey.tar.gz" | sha256sum -c - && \
        \
        tar xzvf "/tmp/limesurvey.tar.gz" --strip-components=1 -C /var/www/html/ && \
        \
        rm -rf "/tmp/limesurvey.tar.gz" \
        /var/www/html/docs \
        /var/www/html/tests \
        /var/www/html/*.md && \
        chown -R "${USER}:root" /var/www/ ; \
        chmod -R g=u /var/www

EXPOSE 9000

WORKDIR /var/www/html
COPY entrypoint.sh entrypoint.sh
USER $USER
ENTRYPOINT ["/var/www/html/entrypoint.sh"]
CMD ["php-fpm"]
