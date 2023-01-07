# CTF
Setup for some CTFs to used by the UNH cyber club
## CTFd
AWS Debian t2.micro

sudo -i # if you do the command iteractively  
#!/bin/sh # if you elect to use UserData at launch time  

```
apt upgrade -y
apt update -y
update-alternatives --install /usr/bin/python python /usr/bin/python3.10 2
apt install git docker docker-compose
systemctl start docker
git clone https://github.com/CTFd/CTFd.git
cd CTFd
./prepare.sh
docker-compose up
```

## Gencybercoin
Based on the excellent work by [Vitaly Ford](https://github.com/vitalyford/gencybercoin)  
Dockerize instruction now that Heroku limits free dynos [if new to AWS and Docker](https://www.cyberciti.biz/faq/how-to-install-docker-on-amazon-linux-2/)  
I put these commands into an AWS template, AWS Linux, t2micro works great.  
```
#!/bin/sh
yum -y update
yum -y install docker python3-pip 
pip3 install --user docker-compose
usermod -a -G docker ec2-user
id ec2-user
newgrp docker
systemctl enable docker.service
systemctl start docker.service
# My generic AWS Docker template stops here
wget https://github.com/vitalyford/gencybercoin/archive/refs/heads/master.zip
unzip master.zip
cd gencybercoin-master/
docker-compose up >/tmp/gencybercoin.log &
# Web page exposed on port 80
```


## Juice Shop
Open publicly shared instance by author: https://juice-shop.herokuapp.com/#/
Heruko button to run your own instance: https://pwning.owasp-juice.shop/part1/running.html
Solutions if you are stuck: https://pwning.owasp-juice.shop/appendix/solutions.html

## Security Shepherd
AWS hosted using an Ubuntu t2.micro image
sudo -i # if you do the command iteractively  
#!/bin/sh # if you elect to use UserData at launch time  

```
apt upgrade -y
apt update -y
apt install git maven docker docker-compose openjdk-8-jdk
git clone https://github.com/OWASP/SecurityShepherd.git
cd SecurityShepherd
gpasswd -a $USER docker
mvn -Pdocker clean install -DskipTests
systemctl start docker
sudo docker-compose up
```

Username=admin, password=password
HTTP is usable but SSL/TLS will be broken at this point
