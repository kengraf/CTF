# CTF
Setup for a some CTFs to used by the UNH cyber club

## Juice Shop
General/shared running instance: https://juice-shop.herokuapp.com/#/
Heruko button: https://pwning.owasp-juice.shop/part1/running.html
Solutions if you stuck: https://pwning.owasp-juice.shop/appendix/solutions.html

## Security Shepherd
AWS hosted using an Ubuntu t2.micro image
```
# Update instance
sudo apt upgrade
sudo apt update

# Install pre-reqs
sudo apt install git maven docker docker-compose openjdk-8-jdk

# Clone the github repository
git clone https://github.com/OWASP/SecurityShepherd.git

# Change directory into the local copy of the repository
cd SecurityShepherd

# Adds current user to the docker group (don't have to run docker with sudo)
sudo gpasswd -a $USER docker

# Run maven to generate the WAR and HTTPS Cert.
mvn -Pdocker clean install -DskipTests
sudo systemctl start docker

# Build the docker images, docker network and bring up the environment
sudo docker-compose up
```

Username=admin, password=password
HTTP is usable but SSL/TLS will be broken at this point
