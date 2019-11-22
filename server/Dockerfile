FROM ubuntu:18.04

ENV TZ=America/Argentina/Buenos_Aires

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt update

# Install tools to add repository for php 7.3
RUN apt install -y software-properties-common build-essential

# Add php repository
RUN add-apt-repository ppa:ondrej/php

# Update dependencies
RUN apt update

# Installs apache2 and php fpm 7.3
RUN apt install -y apache2 php7.3-fpm

# Enable several mods in apache
RUN a2enmod proxy_fcgi setenvif rewrite http2 headers

# Enable php fpm in apache
RUN a2enconf php7.3-fpm

# Install some basic libs, composer and supervisor
RUN DEBIAN_FRONTEND=noninteractive apt install -y php7.3-mysql php7.3-xml php7.3-curl php7.3-zip php7.3-mbstring supervisor

# Install libraries to allow pecl installs
RUN apt install -y php-pear php7.3-dev

# Install xdebug
RUN pecl install -f xdebug

# Install composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install prestissimo for parallel downloads in composer
RUN composer global require hirak/prestissimo

# Create a vhost with h2 poiting to /var/www/public
COPY ["server/000-default.conf", "/etc/apache2/sites-available/000-default.conf"]

RUN service php7.3-fpm start

RUN service apache2 start

# Copy the supervisor configuration to run apache and php-fpm in parallel
COPY ["server/supervisord.conf", "/etc/supervisor/conf.d/apache-with-fpm.conf"]

CMD ["/usr/bin/supervisord", "-n"]