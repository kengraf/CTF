# CTF
Setup for some CTFs to used by the UNH cyber club
## CTFd
AWS Linux 2023 t2.micro

sudo -i # if you do the command iteractively  
#!/bin/sh # if you elect to use UserData at launch time  

```
yum update -y
yum install docker -y
yum install git -y
usermod -a -G docker ec2-user
systemctl start docker
docker info
cd /usr/local/bin
curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-Linux-x86_64" -o docker-compose
chmod +x docker-compose
cd /
git clone https://github.com/CTFd/CTFd.git
# Fix YML
cd /CTFd
sed -i "1i version: '3'" docker-compose.yml
# end fix

echo "cd /CTFd" >> /etc/rc.d/rc.local
echo "systemctl start docker" >> /etc/rc.d/rc.local
echo "docker-compose up > /CTFd/ctfd.log &" >> /etc/rc.d/rc.local
chmod +x /etc/rc.d/rc.local
reboot
# To import a known set of challenges import the following
# https://s3.amazonaws.com/cyber-unh.org/CTFD-GenCyber-2023-01-02.zip
# This will set the admin user as: admin/password
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


# PROJECT template
```
#!/bin/bash
yum -y update

# Setup web server
yum -y install nginx
cd /usr/share/nginx/html
cat <<EOT > index.html
<!-- Yoda’s password is a word on this page -->
<h1>Straight shooter</h1>
<h3>Yoda may be a good billiard instructor, but he is blissfully security ignorant</h3>
EOT
/bin/systemctl start nginx.service

# setup fswatch (revisit use)
yum install -y gcc-c++
cd /tmp
wget https://github.com/emcrisostomo/fswatch/releases/download/1.17.1/fswatch-1.17.1.tar.gz
tar -xvf fswatch-1.17.1.tar.gz
cd fswatch-1.17.1
./configure
make
make install

# Install inotifywait
# To monitor in a shell: inotifywait -r -m -e access /home
yum install inotify-tools -y 

# Setup SSH activity  shell
cat <<EOT > /var/log/ssh_activity.sh
grep -o -E '[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}.[0-9]{1,3}' \$1 | sort -g | uniq -c | sort -g
grep -o -E  '[iI]nvalid user [a-z]* ' \$1 | tr -s ' ' | cut -f 3 -d ' ' | sort | uniq -c | sort -g
grep Accepted \$1 | tr -s ' ' | cut -d ' ' -f 1,2,3,7,9
echo "failed attempts \$(grep -o -E '[iI]nvalid user [a-z]' \$1 | wc | awk '{ printf \$1; }')"
EOT

# Create user
useradd --password $(openssl passwd billiard) yoda

# Allow password login
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
/bin/systemctl restart sshd

# Setup ssh hack, ec2-user keys available to everyone
chmod 755 /home/ec2-user
cd /home/ec2-user/
ssh-keygen -b 2048 -f id_rsa -N ""
cat id_rsa.pub >> .ssh/authorized_keys
chmod 644 id_rsa
chmod 755 /home/*
echo "Need to properly set the file location and permissions for ec2-user's SSH keys" > TODO

# Add the root flag
echo "AA2E22F35FA4534B" > /root/root.txt```
