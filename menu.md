# How to build a wordpress system use nginx php-fpm mysql nfs docker
* keywords：nginx php-fpm mysql nfs docker


## 1. System parameter

* Cloud Plat：Microsoft azure 
* OS system: Ubuntu Description: Ubuntu 16.04.5 LTS Release: 16.04 Codename: xenial
* Hardware：1 cpu,memory 2G
* Docker Version:Docker-ce=17.03.2~ce
* [How to install docker docker-compose](/content/how_to_install.md)

## 2. Steps to bulid a wordpress system

* Whole framework

    ![Whole framework](/images/steps_whole_framework.png)
* [Step one: Build two volumes and network in docker](/content/step1.md)
* [Step two: Build nfs-server and copy wordpress](/content/step2.md)
* [Step three: Build mysql and phpmyadmin](/content/step3.md)
* [Step four: Build php-fpm](/content/step4.md)
* [Step five: Build nginx](/content/step5.md)



## Reference
1. [Nginx动静分离操作讲解](http://www.php.cn/php-weizijiaocheng-390516.html)
