# Introduction
These are my notes to install a full blown mean stack (MongoDB, Express, Angular4, Node) on the pi, including git, some build and deploy tool. I keep these notes to be able to quickly set up, or reinstall my environment. 

# Table of Contents
1. [Setup](#setup)
2. [Sources](#sources)


Probably I'm still working on it, but this is how far I came, or care to write down.

## Setup
Back to [Table of Contents](#table-of-contents)

* Raspberry pi - ehm... model 3 Model B ![used procmeminfo to find out that I have ][cloud]  

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


# Sources
Back to [Table of Contents](#table-of-contents)

## Pi
* Hardware information using `cat /proc/cpuinfo | grep 'Revision' | awk '{print $3}' | sed 's/^1000//'` and [this](http://elinux.org/RPi_HardwareHistory) table

## Git
* Some [problem I had to solve](https://stackoverflow.com/questions/11621768/how-can-i-make-git-accept-a-self-signed-certificate) with git that didn't accept self signed certificate with `git config http.sslVerify false`. Probably not a good way to solve it, but it worked.
* [forgot why](http://www.instructables.com/id/GitPi-A-Private-Git-Server-on-Raspberry-Pi/)

## Markdown
* I didn't remember all markdown tricks used so I used the [Markdown Guide](https://help.ghost.org/hc/en-us/articles/224410728-Markdown-Guide)
* This [resource](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) is a [Cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) that has all tricks you can do that works on github

## MongoDB
* [forgot why](http://yannickloriot.com/2016/04/install-mongodb-and-node-js-on-a-raspberry-pi/)

## MEAN
* [forgot why](https://www.toptal.com/raspberry-pi/how-to-turn-your-raspberry-pi-into-a-development-server)

## Continuous Integration
* [forgot why](http://thesociablegeek.com/node/github-continuous-deployment-to-a-raspberry-pi/)

# Images
* ![alt text][cloud] `![alt text][cloud]` 


[//]: # (R E F E R E N C E S)
[cloud]: http://xgkfq28377.i.lithium.com/skins/images/4625BFF6D7B6FC17E1CAA1067E7B2290/base/images/icon_board.png 


