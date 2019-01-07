# System parameter
1. Plat：azure
  操作系统：Distributor ID: Ubuntu
  Description:    Ubuntu 16.04.5 LTS
  Release:        16.04
  Codename:       xenial
2. Docker-ce=17.03.2~ce

# Step one:  Build one nfs-server

1.1 create a docker-compose.yml
```
mkdir nfs-server

cd nfs-server

vi docker-compose.yml
```
1.2 copy the content of below into the docker-compose.yml
```
version: '2.0'

services:

  nfs-server:
     image: itsthenetwork/nfs-server-alpine:10
     restart: always
     privileged: true
     volumes:
       -   nfs-share:/nfsshare
     environment:
       SHARED_DIRECTORY: /nfsshare
     networks:
       -  net1

volumes:
  nfs-share:

networks:
  net1:
    driver: bridge

```
1.3 start
```
sudo docker-compose up

# inspect the volume

sudo docker volume ls

# insect the networks

sudo docker network ls

```
you will find the “net1” network is show “nfsserver_net1” in the screen,so

if you want to use this network, you should use “nfsserver_net1”


# Step two:  Build one mysql and phpmyadmin
1.1 create a docker-compose.yml
```
mkdir mysql

cd mysql

vi docker-compose.yml

```
1.2 copy the content of below into the docker-compose.yml
```
version: '2.0'

services:
  db:
    image: mysql:5.6
    restart: always
    volumes:
      -   mydb-data:/var/lib/mysql
    environment:
      MYSQL_DATABASE: wordpress_new
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: 123456
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    networks:
       -  nfsserver_net1
  phpmyadmin:
     image: phpmyadmin/phpmyadmin:4.7
     ports:
      - 8080:80
     depends_on:
       - "db"
     environment:
       PMA_HOST: db
     networks:
       -  nfsserver_net1

volumes:
  mydb-data:

networks:
  nfsserver_net1:
     external: true

```
1.3 start
```
sudo docker-compose up

# inspect the volume

sudo docker volume ls

```
# Step three:  Build wordpress
1.1 create a docker-compose.yml
```
mkdir wordpress

cd wordpress

vi docker-compose.yml
```
1.2 copy the content of below into the docker-compose.yml

```
version: '2.0'

services:

  wordpress-new:
    image: wordpress:5.0.0-php7.2-fpm
    privileged: true
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: 123456
      WORDPRESS_DB_NAME: wordpress_new
    networks:
       -  nfsserver_net1
networks:
  nfsserver_net1:
     external: true
```
1.3 start
```
sudo docker-compose up
```
1.4  install nfs-clinet and mount

```
# entance wordpress container
sudo docker exec -it container_name /bin/bash

# mkdir a directory for mount 
mkdir -p /var/www/myhtml


# install some softwave
apt-get -y update && apt-get -y install rpcbind nfs-common nfs4-acl-tools  vim

# config nfs-clinet

vim /etc/default/nfs-common

NEED_STATD=no
STATDOPTS=
NEED_IDMAPD=yes
NEED_GSSD=no

# start 
 
service rpcbind start  #have error information,but it still work well

service nfs-common start


# Configure the mount point in /etc/fstab:
# you must look for the ip addr before in nfs-server

vim /etc/fstab

172.27.0.2:/  /var/www/myhtml  nfs4  sec=sys,noatime  0  0

# mount and vertify

mount -a

df -h

```





# Step four:  Build nginx
1.1 create a docker-compose.yml and ngix,conf
```
mkdir nginx

cd nginx
touch mysite.template
touch docker-compose.yml
```

1.2 copy the content of below into the mysite.template

```
 server {
    listen       80;
    server_name  www.lushatech.com lushatech.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        #root   /usr/share/nginx/html;
        root   /var/www/html;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root           /var/www/html;
        fastcgi_pass   wordpress-new:9000;#if cantwork work IP instead wordpress-new
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}


```

1.3 copy the content of below into the docker-compose.yml

```
version: '2.0'

services:
  nginx:
     image: nginx:1.15.7
     privileged: true
     restart: always
     volumes:
        - ./mysite.template.conf:/etc/nginx/conf.d/mysite.template.conf
     ports:
       - 80:80
     networks:
       -  nfsserver_net1
networks:
   nfsserver_net1:
      external: true
```
1.4 start
```
sudo docker-compose up

````

1.5 install nfs-clinet and mount

```
# entance nginx container
sudo docker exec -it container_name /bin/bash

# mkdir a directory for mount 
mkdir -p /var/www/myhtml


# install some softwave
apt-get -y update && apt-get -y install rpcbind nfs-common nfs4-acl-tools  vim

# config nfs-clinet

vim /etc/default/nfs-common

NEED_STATD=no
STATDOPTS=
NEED_IDMAPD=yes
NEED_GSSD=no

# start 
 
service rpcbind start  #have error information,but it still work well

service nfs-common start


# Configure the mount point in /etc/fstab:
# you must look for the ip addr before in nfs-server

vim /etc/fstab

172.27.0.2:/  /var/www/myhtml  nfs4  sec=sys,noatime  0  0

# mount and vertify

mount -a

df -h

```
