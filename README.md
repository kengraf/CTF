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
