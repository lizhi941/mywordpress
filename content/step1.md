# Step one: create two volumes and one network in docker

Inorder to providing persistent data，create two volumes，one for mysql: vol1-mysql-data, another for NFS: vol2-nfs-data.
Inorder to communictae in the same network,create netwaork: net1.
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



# Create networks
sudo docker network create -d bridge net1

# Vertify
sudo docker network ls
#####show######
NETWORK ID          NAME                DRIVER              SCOPE
58443f8652a2        bridge              bridge              local
34c4df2212cb        host                host                local
59552d2f9ec4        net1                bridge              local
ab9d2515dbf4        none                null                local

```
