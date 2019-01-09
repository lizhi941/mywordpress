# Step four: Build php-fpm

## 2.1 Create a Dockerfile file

inorder to install some extensions that need when run wordpress,so build a new image under the php:7.2-fpm images.

```
cd /home/lizhi  # for example the user is lizhi
mkdir php-fpm
cd php-fpm
vi Dockerfile

#####content of Dockerfile start######
FROM php:7.2-fpm

# install the PHP extensions we need
RUN set -ex; \
        \
        savedAptMark="$(apt-mark showmanual)"; \
        \
        apt-get update; \
        apt-get install -y --no-install-recommends \
                libjpeg-dev \
                libpng-dev \
        ; \
        \
        docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr; \
        docker-php-ext-install gd mysqli opcache zip; \
        \
# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
        apt-mark auto '.*' > /dev/null; \
        apt-mark manual $savedAptMark; \
        ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
                | awk '/=>/ { print $3 }' \
                | sort -u \
                | xargs -r dpkg-query -S \
                | cut -d: -f1 \
                | sort -u \
                | xargs -rt apt-mark manual; \
        \
        apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
        rm -rf /var/lib/apt/lists/*

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
                echo 'opcache.memory_consumption=128'; \
                echo 'opcache.interned_strings_buffer=8'; \
                echo 'opcache.max_accelerated_files=4000'; \
                echo 'opcache.revalidate_freq=2'; \
                echo 'opcache.fast_shutdown=1'; \
                echo 'opcache.enable_cli=1'; \
        } > /usr/local/etc/php/conf.d/opcache-recommended.ini

VOLUME /var/www/html

CMD ["php-fpm"]
#####content of Dockerfile end######
```

## 2.2 Build a image lizhi/wordpress/php-7.2-fpm:v1.0

```
sudo docker build -t lizhi/wordpress/php-7.2-fpm:v1.0 .

```



## 2.3 Create a docker-compose.yml


```
cd /home/lizhi  # for example the user is lizhi
mkdir php-fpm
cd php-fpm
vi docker-compose.yml

#####content of docker-compose.yml start######
version: '2.0'

services:

  php-fpm:
    image: lizhi/wordpress/php-7.2-fpm:v1.0
    privileged: true
    restart: always
    networks:
       -  net1
networks:
    net1:
     external: true
     
 #####content of docker-compose.yml end######

```
