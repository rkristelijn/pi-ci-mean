# Introduction
These are my notes to install a full blown mean stack (MongoDB, Express, Angular4, Node) on the pi, including git, some build and deploy tool. I keep these notes to be able to quickly set up, or reinstall my environment. 

# Table of Contents
0. [Setup](#setup)
1. [Install OS](#install-os)
  * [Configure OS](#configure-os)
  * [Login remotely](#login-remotely)
  * [Update](#update)
2. [Webserver](#webserver)
3. [MongoDB](#mongodb)
4. [MEAN](#mean)
3. [Sources](#sources)

Probably I'm still working on it, but this is how far I came, or care to write down.

## Setup

Back to [Table of Contents](#table-of-contents)

* Raspberry pi - ehm... *model 3 Model B* (see [Pi](#pi) hardware information in [Sources](#sources))
* Network - wired
* Mode - headless (no monitor, nor keyboard)
* OS: 

# Install OS

Back to [Table of Contents](#table-of-contents)

Connect HDMI/keyboard/mouse, type `sudo reboot` or start the Pi, then hold shift to start in NOOBS. My current version is v1.9 (Mar 18 2016) and I know there's a new NOOBS version, but I only need to install Raspbian Lite (A port of Debian jessie for the Raspberry Pi (minimal version), so no overhead of GUI. After all... I'm not a *User* that needs a graphical interface. I just want to run the server as slim as possible.

**In short**:

1. Start in NOOBS by holding `SHIFT`
2. Choose Raspbian Lite
3. Overwrite current OS: Y
4. Wait and reboot when asked (`Ok)`

## Configure OS

Back to [Table of Contents](#table-of-contents)

After reboot the system comes up, autodetects LAN networking, IP, etc and starts with a prompt for login. After login, we start the configuration menu for changing the password and the hostname. This will both update `/etc/hosts` and `/etc/hostname`.

**In short**:

1. Login using user:`pi` and password `raspberry`
2. Start the config menu: `sudo raspi-config`
3. Change default password 
4. Change hostname to `1337-pi`
5. Boot Options > Desktop / CLI > Console Autologin
6. Interfacing Options > SSH > Yes, Enable SSH Server

because of NOOBS:

7. Advanced > Expand Filesystem

## Login Remotely

Back to [Table of Contents](#table-of-contents)

Install putty and connect to your Pi. If that works, it is time to relocate the Pi to run headless. You don't need the keyboard/mouse/monitor to further configure, you do this remotely. Because my router that takes care of handing the IP addresses (DHCP) and of the hostname-IP-address translation (DNS), I can always find my Pi using `hostname.local` in both the browser and other tools. 

**In short**:

1. Connect with putty
  * Host Name (or IP address): `1337-pi.local`
  * Port: `22` (default)
  * Button: `Open`
  * Accept certificate
2. After checking SSH works;
  * shutdown Pi using `sudo halt -p`
  * Move Pi to secure place to run in headless mode

## Update 

Back to [Table of Contents](#table-of-contents)

To make sure you are up to date with the software that we are going to install using `apt-get`. Next to that we need a proper editor, which is vim.

**In Short**:

1. `sudo apt-get update`
2. `sudo apt-get upgrade`
3. `sudo apt-get install vim`

# Webserver

Back to [Table of Contents](#table-of-contents)

You could use only Node as a webserver however it seems [best practice](https://www.nginx.com/blog/5-performance-tips-for-node-js-applications/) to also install nginx.

For now I'm going only install Node, because I don't expose my site to the web. The problem is that Node is not Nodejs. Nodejs is the one we need, not sure what Node is.

We need to create a symlink to use `node` as a command, and not having to type `nodejs` using `sudo ln -s ``which nodejs`` /usr/local/bin/node` acording to this [page](https://stackoverflow.com/questions/20057790/what-are-the-differences-between-node-js-and-node). 

Next to that we need to install Node Package Manager. I wonder if I had installed NPM first. Now I have:

    pi@1337-pi:~ $ npm -v
    1.4.21
    pi@1337-pi:~ $ node -v
    v0.10.29

That's weird, the latest version of node is 8.1.2. Let's upgrade that.

    pi@1337-pi:~ $ sudo npm install n -g
    /usr/local/bin/n -> /usr/local/lib/node_modules/n/bin/n
    n@2.1.7 /usr/local/lib/node_modules/n
    pi@1337-pi:~ $ sudo n stable
    
     install : node-v8.0.0
       mkdir : /usr/local/n/versions/node/8.0.0
       fetch : https://nodejs.org/dist/v8.0.0/node-v8.0.0-linux-armv7l.tar.gz
    ######################################################################## 100,0%
       installed : v8.0.0
    

Now we need to create a `package.json` file (`npm init`). After that we create the `nodes_modules` folder using `npm install`. Then we create a small hello world program

    var http = require('http');
    http.createServer( function (req, res) {
      res.writeHead(200, {'Content-Type': 'text/plain'});
      res.end('Hello world');
    }).listen(80);
    console.log('Server running');
    

**In short**:

Installation

1. `sudo apt-get install nodejs`
2. `sudo ln -s ``which nodejs`` /usr/local/bin/node`
3. `sudo apt-get install npm`

Create new project in `/home/pi`

4. `cd /home/pi`
4. `npm init`
6. `npm install`
7. `vim app.js` paste contents

Run the server

8. `sudo node app.js` - for ports below 128 we need to use sudo

Test

9. navigate the browser to `1337-pi.local`

# MongoDB

Back to [Table of Contents](#table-of-contents)

## Installation

For the MEAN-stack we need Mongodb. Install using `sudo apt-get install mongodb-server`.  

## Test
Start the service using `sudo service mogodb start`. After that you can use `mongo` and exit.

To create a database in MongoDB, start by creating a MongoClient object, then specify a connection URL with the correct ip address and the name of the database you want to create.

MongoDB will create the database if it does not exist, and make a connection to it.

For a node test, use this script:

	var client = require('mongodb').MongoClient;
	var url = "mongodb://localhost:27017/mydb";
	
	client.connect(url, function(err, db) {
	  if (err) throw err;
	  console.log("Database created!");
	  db.close();
	});

** In short **:

1. `sudo apt-get install mongodb-server`
2. `sudo service mogodb start`
3. `mongo`
4. `cd /home/pi`
5. `vim mongodb.js`, paste code, save and exit
6. `sudo node mongodb.js`

# MEAN

Back to [Table of Contents](#table-of-contents)

We don't want to be editing config files manually, I mean, we could, however it is easy to make a mistake. We just use a scaffolding tool like [meanjs](http://meanjs.org/generator.html). For Yo we need a git client.

1. `cd /home/pi`
2. `rm -rf *`
3. `sudo apt-get install git`
2. `sudo npm install -g yo`
2. `sudo npm install -g generator-meanjs`
3. `yo meanjs`
	* version: 0.4.2 
	* location: mean (default)
	* application name: MEAN (default)
	* description: (default)
	* default
	* default
	* generate sample CRUD: Y
	* generate sample chat: Y
4. `grunt`

# Run all as services on boot time

Back to [Table of Contents](#table-of-contents)

Todo: see [this](http://yannickloriot.com/2016/04/install-mongodb-and-node-js-on-a-raspberry-pi/)

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
* [Install MongoDB](http://yannickloriot.com/2016/04/install-mongodb-and-node-js-on-a-raspberry-pi/)

## MEAN
* [forgot why](https://www.toptal.com/raspberry-pi/how-to-turn-your-raspberry-pi-into-a-development-server)

## Continuous Integration
* [forgot why](http://thesociablegeek.com/node/github-continuous-deployment-to-a-raspberry-pi/)

## TODO: Notes

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