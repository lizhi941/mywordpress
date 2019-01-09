# How to install

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

## how to install docker17.03.2

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
