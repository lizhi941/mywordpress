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

volumes:
  nfs-share:

```
1.3 start
```
sudo docker-compose up

# inspect the volume

sudo docker volume ls

```

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
  
  phpmyadmin:
     image: phpmyadmin/phpmyadmin:4.7
     ports:
      - 8080:80
     depends_on:
       - "db"
     command: ["./wait-for-it.sh", "db:3306", "--", "python", "app.py"]
     environment:
       PMA_HOST: db
     

volumes:
  mydb-data:

```

1.3


