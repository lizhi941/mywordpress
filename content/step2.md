# Step two: Build one nfs-server and copy wordpress

## 2.1 Create a docker-compose.yml

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

## 2.2 start 

```
sudo docker-compose up

```
## 2.3 download and copy wordpress into nfs-server's share directory

```
cd /home/lizhi  # for example the user is lizhi

cd nfs-server

# Download wordpress and tar 
curl -o   wordpress-5.0.2.tar.gz        https://wordpress.org/wordpress-5.0.2.tar.gz
tar -xzvf  wordpress-5.0.2.tar.gz  
mv wordpress   wordpress5.0.2


# Entrance the container and mkdir wordpress
sudo docker exec -it container_id /bin/bash
cd /nfsshare
mkdir wordpress
exit       # exit the container  back to the host


# copy the wordpress5.0.2  into the  container
sudo docker cp /home/lizhi/nfs-server/wordpress5.0.2   container_id:/nfsshare/wordpress/

# verify
sudo docker exec -it container_id /bin/bash
cd /nfsshare/wordpress/
ls -al
#####show#####
wordpress5.0.2


```

