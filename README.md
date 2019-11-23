# php-fpm-apache-docker
Another codebase of Docker image for php with fpm and apache2 web server containerization

## Docker Hub image link
https://hub.docker.com/repository/docker/mrtnzagustin/apache-phpfpm

## With docker-compose
```yaml
services:  
  server:
    image: mrtnzagustin/apache-phpfpm:7.3-ubuntu        
    ports:      
      - "8282:80" 
```
## Basic Override
### With Dockerfile
```Dockerfile
FROM mrtnzagustin/apache-phpfpm:7.3-ubuntu
# Do some stuff -> copy files, install depedencies, install other packages, enable xdebug, etc
# Restart services
RUN service php7.3-fpm restart
RUN service apache2 restart
```
### Virtual Host
A basic host is pointed to `/var/www/public` folder. If you want just to override this unique vhost you should replace `/etc/apache2/sites-available/000-default.conf`. For example:
```Dockerfile
COPY ["my-folder/my-vhost.conf", "/etc/apache2/sites-available/000-default.conf"]
```

### Enabling xdebug
Xdebug is installed but not enabled. To do so, just add to your Dockerfile:
```Dockerfile
RUN echo "zend_extension=$(find /usr/lib/php/ -name xdebug.so)" > /etc/php/7.3/fpm/conf.d/xdebug.ini
```
You also can use an argument in your docker-compsoe file to enabling it per environment base:
In docker
```Dockerfile
ARG environment
RUN if [ "$environment" = "dev" ] ; then echo "zend_extension=$(find /usr/lib/php/ -name xdebug.so)" > /etc/php/7.3/fpm/conf.d/xdebug.ini; fi
```
### Installing project dependencies (example with composer [composer is pre installed])
```Dockerfile
# Copy your composer files
COPY ["composer.json", "/var/www/composer.json"]

# Install dependencies
RUN cd /var/www ; \	
 	composer install
```
### Extra config to php.ini
```Dockerfile
COPY ["my-folder/my-extra.ini","/etc/php/7.3/fpm/conf.d/extra.ini"]
```
## Other relevant information
## Port 80
The server runs in port 80

