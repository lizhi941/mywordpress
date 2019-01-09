# step three：bulid mysql and phpmyadmin

## 3.1 Create a docker-compose.yml

```
cd /home/lizhi  # for example the user is lizhi

mkdir mysql

cd mysql

vi docker-compose.yml

#####content of docker-compose.yml######
version: '2.0'

services:
  db:
    image: mysql:5.6
    restart: always
    volumes:
      -   vol1-mysql-data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    networks:
       -  net1
  phpmyadmin:
     image: phpmyadmin/phpmyadmin:4.7
     ports:
      - 8080:80
     depends_on:
       - "db"
     environment:
       PMA_HOST: db
     networks:
       -  net1
volumes:
     vol2-mysql-data:
        external: true

networks:
    net1:
       external: true



```


## 3.2 start

```
sudo docker-compose up
```
