
FROM php:7.4-apache-buster

# Microsoft SQL Server Prerequisites
ENV ACCEPT_EULA=Y
RUN apt-get update \
    && apt-get -y --no-install-recommends install apt-utils libxml2-dev gnupg \
        apt-transport-https libzip-dev zip \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/10/prod.list \
        > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && apt-get -y --no-install-recommends install \
        unixodbc-dev \
        msodbcsql17
RUN docker-php-ext-install zip
RUN pecl install sqlsrv-5.10.1 \
    && pecl install pdo_sqlsrv-5.10.1
RUN docker-php-ext-enable sqlsrv pdo_sqlsrv zip
RUN apt-get update -yqq \
    && apt-get install -y --no-install-recommends openssl \ 
    && sed -i 's,^\(MinProtocol[ ]*=\).*,\1'TLSv1.0',g' /etc/ssl/openssl.cnf \
    && sed -i 's,^\(CipherString[ ]*=\).*,\1'DEFAULT@SECLEVEL=1',g' /etc/ssl/openssl.cnf \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* 

RUN docker-php-ext-install mysqli 
RUN docker-php-ext-enable mysqli

# xdebug
RUN pecl install xdebug-3.1.6 
RUN docker-php-ext-enable xdebug

# php-gd extension
RUN apt-get update -y && apt-get install -y libwebp-dev libjpeg62-turbo-dev libpng-dev libxpm-dev \
    libfreetype6-dev
RUN apt-get update && \
    apt-get install -y \
        zlib1g-dev 
# RUN docker-php-ext-install mbstring
RUN apt-get install -y libzip-dev
RUN docker-php-ext-install zip
RUN docker-php-ext-configure gd 
RUN docker-php-ext-install gd

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN a2enmod rewrite \
    && apache2ctl restart

WORKDIR /var/www/html