# Step one: Build one nfs-server

## 1.1 Create a docker-compose.yml

```
#entrance the user home directory
cd /home/lizhi  # for example the user is lizhi

mkdir nfs-server

cd nfs-server

vi docker-compose.yml

#####content of docker-compose.yml######
version: '2.0'

services:

  nfs-server:
     image: itsthenetwork/nfs-server-alpine:10
     restart: always
     privileged: true
     volumes:
       -   vol2-nfs-data:/nfsshare
     environment:
       SHARED_DIRECTORY: /nfsshare
     networks:
       -  net1
volumes:
     vol2-nfs-data:
        external: true

networks:
    net1:
       external: true
```

## 1.2 start 

```
sudo docker-compose up

```
