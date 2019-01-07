# Step one  Build one nfs-server

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
