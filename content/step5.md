# Step five：Build nginx

## 4.1 Create a docker-compose.yml mysite-wordpress.conf

```
#entrance the user home directory
cd /home/lizhi  # for example the user is lizhi

mkdir nginx

cd nginx

touch mysite-wordpress.conf
touch docker-compose.yml


#####content of mysite-wordpress.conf######
server {
    listen       80;
    server_name  new.lushatech.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /var/www/html/wordpress5.0.2;
        index  index.html index.htm index.php;
        try_files $uri $uri/ /index.php?$args;
    }
   
    rewrite /wp-admin$ $scheme://$host$uri/ permanent;



    location ~* ^.+\.(ogg|ogv|svg|svgz|eot|otf|woff|mp4|ttf|rss|atom|jpg|jpeg|gif|png|ico|zip|tgz|gz|rar|bz2|doc|xls|exe|ppt|tar|mid|midi|wav|bmp|rtf)$ {
            access_log off; log_not_found off; expires max;
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
        root           /var/www/html/wordpress5.0.2;
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass   172.27.0.7:9000;
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



#####content of docker-compose.yml######

version: '2.0'

services:
  nginx:
     image: nginx:1.15.7
     privileged: true
     restart: always
     volumes:
        - ./mysite-wordpress.conf:/etc/nginx/conf.d/mysite-wordpress.conf
     ports:
       - 80:80
     networks:
       -  net1
networks:
   net1:
      external: true
```
