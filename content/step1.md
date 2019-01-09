# Create two volumes in docker

Inorder to providing persistent data，create two volumes，one for mysql: vol1-mysql-data, another for NFS: vol2-nfs-data.

```
# Create volumes
sudo docker volume create --name vol1-mysql-data
sudo docker volume create --name vol2-nfs-data

# Vertify
sudo docker volume ls
#####show######
DRIVER              VOLUME NAME
local               vol1-mysql-data
local               vol2-nfs-data

```
