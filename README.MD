# Introduction
These are my notes to install a full blown mean stack (MongoDB, Express, Angular4, Node) on the pi, including git, some build and deploy tool. I keep these notes to be able to quickly set up, or reinstall my environment. 

# Table of Contents
1. [Set-up](#Set-up)
2. [Example2](#example2)
3. [Third Example](#third-example)


Probably I'm still working on it, but this is how far I came, or care to write down.

##Set-up:
* Raspberry pi - ehm...  

install raspbian cli
sudo raspi-config

* change hostname to 1337-pi
* change password to something else than the default raspberry

sudo apt-get update
sudo apt-get upgrade
sudo apt-get install nginx
; nginx for static files, node for applications; seems better to split this

; node
wget http://node-arm.herokuapp.com/node_latest_armhf.deb
sudo dpkg -i node_latest_armhf.deb
; is this the best way?
; alternative:
wget https://nodejs.org/dist/latest-v5.x/node-v5.11.0-linux-armv7l.tar.gz
tar -xvzf node-v5.11.0-linux-armv7l.tar.gz
sudo mv node-v5.11.0-linux-armv7l /opt/node
sudo mkdir /opt/bin
sudo ln -s /opt/node/bin/* /opt/bin/
; add to path
sudo nano /etc/profile
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/bin"

; update npm
npm install --save latest-version

; mongodb
; now mongodb is integrated on arm, was not before
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install mongodb-server

; optional:
npm install -g generator-angular-fullstack
mkdir my-app
cd my-app
yo angular-fullstack my-app

; todo: git server, auto build etc
git init
git remote add origin git@bitbucket.org:USER/REPO.git
git add .
git commit -m 'Initial commit'
git push -u origin master


#Sources
http://yannickloriot.com/2016/04/install-mongodb-and-node-js-on-a-raspberry-pi/
https://www.toptal.com/raspberry-pi/how-to-turn-your-raspberry-pi-into-a-development-server

;ci
http://thesociablegeek.com/node/github-continuous-deployment-to-a-raspberry-pi/

;git
http://www.instructables.com/id/GitPi-A-Private-Git-Server-on-Raspberry-Pi/