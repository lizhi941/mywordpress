# Step four: Build php-fpm

## 4.1 Create a Dockerfile file

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

## 4.2 Build a image lizhi/wordpress/php-7.2-fpm:v1.0

```
sudo docker build -t lizhi/wordpress/php-7.2-fpm:v1.0 .

```



## 4.3 Create a docker-compose.yml


```
cd /home/lizhi  # for example the user is lizhi
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
 
 
sudo docker-compose up

```

## 4.4 Install nfs-clinet and mount in the php-fpm container

```
sudo docker exec -it container_id /bin/bash

# install some softwave
apt-get -y update && apt-get -y install rpcbind nfs-common nfs4-acl-tools  vim

# config nfs-clinet
vim /etc/default/nfs-common

######contnet of nfs-common start######
NEED_STATD=no
STATDOPTS=
NEED_IDMAPD=yes
NEED_GSSD=no
######contnet of nfs-common end######

# start service
service rpcbind start      #have error information,but it still work well
service nfs-common start

# Configure the mount point in /etc/fstab:
# you must look for the ip addr before in nfs-server
#you can also use the service name(for example nfs-server) instead of the ip,because they are in the same network.
vim /etc/fstab
######contnet of fstab start######
172.18.0.2:/wordpress  /var/www/html  nfs4  sec=sys,noatime  0  0


######contnet of fstab end######

# mount and vertify
mount -a

df -h

# add user and group www-data

chown -R www-data:www-data /var/www/html

#####note start######
Here we changed the /var/www/html of user and group to www-data:www-data.
Because the php-fpm is run in www-data:www-data.
But it is only effect in php-fpm container and dont change the user and group in the nfs-server
and dont effect the other container that mount on the nfs-server.
#####note end######

```

 #  if you want to run a different php-fpm version(for example php5.6-fpm)，you can use php5.6-fpm instead of php7.2-fpm in the steps above.
