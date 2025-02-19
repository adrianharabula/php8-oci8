FROM php:8.2-apache

RUN apt-get update && apt-get install -y \
        unzip \
        libfreetype-dev \
        libjpeg62-turbo-dev \
        # libmcrypt-dev \
        libpng-dev \
        libaio1 \
    && docker-php-ext-install -j$(nproc) iconv gettext \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd

RUN curl -L https://raw.github.com/adrianharabula/php7-with-oci8/master/docker-php.conf -o /etc/apache2/conf-enabled/docker-php.conf

RUN printf "log_errors = On \nerror_log = /dev/stderr\n" > /usr/local/etc/php/conf.d/php-logs.ini

RUN a2enmod rewrite

RUN cd /tmp && curl -L https://raw.github.com/adrianharabula/php8-oci8/master/instantclient/21.12.0.0.0/instantclient-basiclite-linux.x64-21.12.0.0.0dbru.zip -O
RUN cd /tmp && curl -L https://raw.github.com/adrianharabula/php8-oci8/master/instantclient/21.12.0.0.0/instantclient-sdk-linux.x64-21.12.0.0.0dbru.zip -O
RUN cd /tmp && curl -L https://raw.github.com/adrianharabula/php8-oci8/master/instantclient/21.12.0.0.0/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip -O

RUN unzip /tmp/instantclient-basiclite-linux.x64-21.12.0.0.0dbru.zip -d /usr/local/
RUN unzip /tmp/instantclient-sdk-linux.x64-21.12.0.0.0dbru.zip -d /usr/local/
RUN unzip /tmp/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip -d /usr/local/

RUN ln -s /usr/local/instantclient_21_12 /usr/local/instantclient
# fixes error "libnnz19.so: cannot open shared object file: No such file or directory"
RUN ln -s /usr/local/instantclient/lib* /usr/lib
RUN ln -s /usr/local/instantclient/sqlplus /usr/bin/sqlplus

RUN echo 'export LD_LIBRARY_PATH="/usr/local/instantclient"' >> /root/.bashrc
RUN echo 'umask 002' >> /root/.bashrc

RUN echo 'instantclient,/usr/local/instantclient' | pecl install oci8
RUN echo "extension=oci8.so" > /usr/local/etc/php/conf.d/php-oci8.ini

RUN echo "<?php echo phpinfo(); ?>" > /var/www/html/phpinfo.php

EXPOSE 80
