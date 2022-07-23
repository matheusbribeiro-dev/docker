FROM php:8.1.7-apache-bullseye

ENV ACCEPT_EULA=Y

RUN a2enmod ssl \
    && a2enmod rewrite
RUN apt-get update && apt-get install -y \
    apt-transport-https \
    gnupg2 \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    nodejs \
    npm \
    bzr \
    cvs \
    subversion \
    wget \
    && rm -rf /var/lib/apt/lists/*
#Instalando php Composer
RUN wget -O composer-setup.php https://getcomposer.org/installer \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && chmod +x /usr/local/bin/composer
# Install prerequisites for the sqlsrv and pdo_sqlsrv PHP extensions.
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/11/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && apt-get install -y msodbcsql17 mssql-tools unixodbc-dev \
    && rm -rf /var/lib/apt/lists/*
# Install PHP extensions
RUN docker-php-ext-install mbstring exif pcntl bcmath gd \
    && rm -rf /var/lib/apt/lists/*
	
# Retrieve the script used to install PHP extensions from the source container.
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/bin/install-php-extensions
# Habilita no PHP.ini. PHP extensions and all their prerequisites available via apt.
RUN chmod uga+x /usr/bin/install-php-extensions \
    && sync \
    && install-php-extensions bcmath ds exif gd intl opcache pcntl pdo_sqlsrv redis sqlsrv zip \
    && rm -rf /var/lib/apt/lists/*
WORKDIR /var/www/html/

EXPOSE 80
CMD ["apachectl", "-D", "FOREGROUND"]
