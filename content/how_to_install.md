# How to install Docker in Azure-cloud  with Ubuntu Server 16.04 LTS


## Create a virtual machine in the Azure-cloud

* [Azure-cloud](https://portal.azure.com)

* INSTANCE DETAILS

Virtual machine name: mywordpress-test
Region: Japan West
Availability options: No infranstructure redundancy required
Image: Ubuntu Server 16.04 LTS
Size: B1ms(VCPU1,RAM2GB,COST/MONTH US$22.32)
DISK OPTIONS: Preminum SSD
Other Options: Default

One the last steps, you will see:
PRODUCT DETAILS
Ubuntu Server 16.04 LTS
by Canonical
Terms of use |  Privacy policy
Pricing not available for this offering
View Pricing details for more information.
Standard B1ms
by Microsoft
Terms of use |  Privacy policy
0.0300 USD/hr
Pricing for other VM sizes


* Click the button "Create" and after a few minutes the VM will run.


## add a disk and mount on

[Azure上如何在Linux下挂载数据磁盘](https://www.cnblogs.com/cloudapps/p/4996276.html)

Because the Azure Linux VM only have 30GB default disk, so we need more disk.

### 1. Add a disk 

Select "Disks tab" On the dashboard of the Linux VM  that you want to add a disk,

Then click "Add data disk", and make some select some options (for example Name, Size ).


### 2. Login in VM and check weather the disk is ok or not

```
sudo fdisk -l

```
if the disk is created , you will see the Disk(for example /dev/sdc).

### 3. Create new partition and file system 

```
sudo fdisk /dev/sdc

#follow the instrument and when finished , you type "w" to exit the operation.
#like this "Command (m for help): w"
```
### 4. Format the disk


```
sudo mkfs -t ext4 /dev/sdc
```

### 5. mount

```
sudo mkdir    /mnt/sdc

sudo mount /dev/sdc   /mnt/sdc

```




## how to install Docker17.03.2 Docker-compose

Connecting virtual machines via SSH

### 1. Query the version of the operating system

```
lsb_release -a

####show####
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 16.04.5 LTS
Release:        16.04
Codename:       xenial
```
### 2. Remove the old docker and set the docker repertory

```
# remove
sudo apt-get remove docker docker-engine docker.io

# set repertory
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo apt-key fingerprint 0EBFCD88


sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update


```

### 3. Install the Docker-ce17.3.2

```
# list the versions of docker-ce can be use
apt-cache madison docker-ce

# select the 17.03.2~ce version
sudo apt-get install docker-ce=17.03.2~ce-0~ubuntu-xenial

# vertify
sudo docker --version

# start

sudo systemctl enable docker

sudo systemctl start docker

```
### 4. Install the Docker-compose

```
sudo apt-get install docker-compose
```

# How to install Docker in VM  with centos7 in my physical machine

[Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)

### Uninstall old versions

```
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
 


###   Install using the repository

#### Set up the repository

1. Install required packages. yum-utils provides the yum-config-manager utility, and device-mapper-persistent-data and lvm2 are required by the devicemapper storage driver.

```
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

```

2. Use the following command to set up the stable repository. You always need the stable repository, even if you want to install builds from the edge or test repositories as well.

```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

```
3. Optional: But here we are not enable the "edge" and "test"
Enable the edge and test repositories. These repositories are included in the docker.repo file above but are disabled by default. You can enable them alongside the stable repository.

```
$ sudo yum-config-manager --enable docker-ce-edge

$ sudo yum-config-manager --enable docker-ce-test

```
You can disable the edge or test repository by running the yum-config-manager command with the --disable flag. To re-enable it, use the --enable flag. The following command disables the edge repository.

```
$ sudo yum-config-manager --disable docker-ce-edge
```
Note: Starting with Docker 17.06, stable releases are also pushed to the edge and test repositories.


#### Install Docker CE（17.03.2，because rancher2.x only support this version ）

1. To install a specific version of Docker CE, list the available versions in the repo, then select and install:
a. List and sort the versions available in your repo. This example sorts results by version number, highest to lowest, and is truncated:

```
$ yum list docker-ce --showduplicates | sort -r

```

2. Install a specific version by its fully qualified package name, which is the package name (docker-ce) plus the version string (2nd column) up to the first hyphen, separated by a hyphen (-), for example, 

```

$ sudo yum install docker-ce-17.03.2.ce

```



