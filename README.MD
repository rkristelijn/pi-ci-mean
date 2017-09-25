# Introduction
These are my notes to install a full blown mean stack (MongoDB, Express, Angular 4+, Node 8+) on the Raspberry Pi. I keep these notes to be able to quickly set up, or reinstall my environment. 

WORK IN PROGRESS

My personal goals: 

* Improve on my full-stack javascript skills by practise
* Prove small and medium companies can benefit from smart solutions with the same functionalties as a full blown ERP or CRM (like Oracle, Microsoft,Salesforce, Workday, SAP etc)
* Redifine the current solution stack: VMWare images (virtualization) running Windows, that run Java (virtualization) that run packaged applications that need to interconnect with different packaged applications via middleware using webservices to create composit applications (virtualization)... Why not one solution for all processes?

This project's goal: Create a platform to minimize cost and powerusage and maximize flexibility, performance and usability.

## The project's offering:

                              +-----+------+-----+
    +------------------+      | SMB | MEAN | git |        +------------+
    |Visual Studio Code|      +-----+------+-----+        |Github.com  |
    +------------------+      |   File System    +-------->            |
    | Windows 10       |      +------------------+        +------------+
    +------------------+------>   Raspberry Pi   |
                              +------------------+

Strengths: Robust, easy to set up
Weaknesses: You need to be able to connect to the Pi, either carry it around using a UTP cross-cable with bridged network, or carry around a switch or hub if you are using an open network. It just doesn't work in my office so my Pi sits at home accessable via port forwarding.


## Alternatives

I have also tried the following offerings:

### Local MEAN stack

    +----------------------------------+
    |Visual Studio Code                |
    +----------------------------------+
    | Windows 10                       |
    |                     +------+-----+
    |                     | MEAN | git |
    +---------------------+------+-----+
    | File System                      |
    +----------------------------------+

Strengths: not so easy to set up
Weaknesses: rebuilding takes a lot of time, test data on local db, need to run servers locally

### Local Git repository

                                            +--------+
                              +-----+------+-v---+    |
    +------------------+      | SMB | MEAN | git +----+
    |Visual Studio Code|      +-----+------+-----+
    +------------------+      |   File System    |
    | Windows 10       |      +------------------+
    +------------------+      |   Raspberry Pi   |
                              +------------------+

Strengths: private code
Weaknesses: If I want to rebuild by Pi, I need additional backup steps

### Virtual Pi

I have tried to use QEMU for a virtual Pi however it is extremely slow because of the mem cap of 256MB of the kernel.

    +----------------------------------+
    |Visual Studio Code                |
    +----------------------------------+
    | Windows 10                       |
    |             QEMU                 |
    |             +-----+------+-----+ |
    |             | SMB | MEAN | git | |
    |             +-----+------+-----+ |
    |             |   File System    | |
    |             +------------------+ |
    |             |   Raspberry Pi   | |
    |             +------------------+ |
    +----------------------------------+

Strengths: Easy backup (7z the image file), easy distribution, no hardware required, portable, no messing around with connectivity issues
Weaknesses: extremely slow

### Cloud

Not yet tried cloud, as my own hosting provider doesn't support node, only PHP. Maybe I'll switch to Heroku, AmazonWS, Google Cloud Platform... in near future. But still I need to have a dev/test environment. These offerings cost money...

Graphics created via [asciiflow.com](http://asciiflow.com/) 

# Table of Contents
0. [Setup](#setup)
1. [Getting Started](#getting-started)
2. [Hardware](#hardware)
2. [Operating System](#operating-system)
3. [Webserver](#webserver)
4. [MongoDB](#mongodb)
5. [MEAN](#mean)
6. [Sources](#sources)

Probably I'm still working on it, but this is how far I came, or care to write down.

## SETUP

Back to [Table of Contents](#table-of-contents)

* Raspberry pi: ehm... *model 3 Model B* (see [Pi](#pi) hardware information in [Sources](#sources))
* Network: wired
* Mode: headless (no monitor, nor keyboard)
* OS: Raspbian Jessy
* Features: Latest MEAN (MongoDB, Express, Angular 4+, Node 8+), Samba, Git (todo: git hook + auto test + auto deploy)

# HARDWARE

Back to [Table of Contents](#table-of-contents)

The Raspberry Pi is a series of small single-board computers developed in the United Kingdom by the Raspberry Pi Foundation to promote the teaching of basic computer science in schools and in developing countries. The original model became far more popular than anticipated, selling outside of its target market for uses such as robotics. Peripherals (including keyboards, mice and cases) are not included with the Raspberry Pi. Some accessories however have been included in several official and unofficial bundles.
According to the Raspberry Pi Foundation, over 5 million Raspberry Pis have been sold before February 2015, making it the best-selling British computer. By November 2016 they had sold 11 million units. [source](https://en.wikipedia.org/wiki/Raspberry_Pi)

The latest model in 2017 is the Pi3. I personally like the Pi in general because smart people put a lot of effort in optimizing the computer board to reduce not only the costs, but indirectly also optimizing squeezing as much as computing power in small circuits. Also there are no moving parts (no fan, no spinning harddisk). The cpu doesn't need cooling, so less power consumption. Also it runs of 5V, so I can power the board from my USB connection. Also there are many applications for the board and the information is all accross the web.

The Pi uses a differt CPU than a plain PC, this is why you are unable to run Windows 10 from a single board. Not sure about a [Pi cluster](http://www.zdnet.com/article/build-your-own-supercomputer-out-of-raspberry-pi-boards/). The CPU used in the Pi is ARM (Advanced RISC Machine). RISC is Reduced Instruction Set Computing, while an x86 processor is CISC (Complex Instruction Set Computing).

Complex Instruction Set Computer (CISC) processors, like the x86, have a rich instruction set capable of doing complex things with a single instruction. Such processors often have significant amounts of internal logic that decode machine instructions to sequences of internal operations (microcode).
RISC architectures, in contrast, have a smaller number of more general purpose instructions, that might be executed with significantly fewer transistors, making the silicon cheaper and more power efficient. Like other RISC architectures, ARM cores have a large number of general-purpose registers and many instructions execute in a single cycle. It has simple addressing modes, where all load/store addresses can be determined from register contents and instruction fields. [source](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.den0013d/index.html)

The core difference between those in this aspect is that ARM instructions operate only on registers with a few instructions for loading and saving data from / to memory while x86 can operate on directly memory as well. Up until v8 ARM was a native 32 bit architecture, favoring four byte operations over others.
So ARM is a simpler architecture, leading to small silicon area and lots of power save features while x86 becoming a power beast in terms of both power consumption and production. [source](https://stackoverflow.com/questions/14794460/how-does-the-arm-architecture-differ-from-x86)

|  Type | A-1 | A-1+ | B 1 | B 1+ | B 2 | B 2 v 1.2 | B 3 | CM 1 | CM 3 | CM 3 Lite | Zero 1.2 | Zero 1.3 | Zero W |
|  ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
|  Target Price ($) | 25 | 20 | 35 | 25 | 35 | 35 | 35 | 30 | 30 | 25 | 5 | 5 | 10 |
|  Clockspeed (MHz) | 700 | 700 | 700 | 700 | 900 | 900 | 1200 | 700 | 1200 | 1200 | 1000 | 1000 | 1000 |
|  Memory | 256 | 512 | 512 | 512 | 1024 | 1024 | 1024 | 512 | 1024 | 1024 | 512 | 512 | 512 |
|  Power (W) | 1,5 | 1 | 3,5 | 1,75 | 4,1 | 4,1 | 6,7 | 1 | 3,5 | 3,5 | 1,75 | 1,75 | 1,75 |
|  Weight (g) | 31 | 23 | 45 | 45 | 45 | 45 | 45 | 7 | 7 | 7 | 9 | 9 | 9 |

Data table to markdown using [google sheet plugin](https://chrome.google.com/webstore/detail/markdowntablemaker/cofkbgfmijanlcdooemafafokhhaeold?hl=en).

# OPERATING SYSTEM

Back to [Table of Contents](#table-of-contents)

## Install OS

Connect HDMI/keyboard/mouse, type `sudo reboot` or start the Pi, then hold shift to start in NOOBS. My current version is v1.9 (Mar 18 2016) and I know there's a new NOOBS version, but I only need to install Raspbian Lite (A port of Debian jessie for the Raspberry Pi (minimal version), so no overhead of GUI. After all... I'm not a *User* that needs a graphical interface. I just want to run the server as slim as possible so all resources go to the server.

**In short**:

1. Start in NOOBS by holding `SHIFT`
2. Choose Raspbian Lite
3. Overwrite current OS: Y
4. Wait and reboot when asked `(Ok)`

## Configure OS

Back to [Table of Contents](#table-of-contents)

After reboot the system comes up, autodetects LAN networking, IP, etc and starts with a prompt for login. After login, we start the configuration menu for changing the password and the hostname. This will both update `/etc/hosts` and `/etc/hostname`.

**In short**:

1. Login using user:`pi` and password `raspberry`
2. Start the config menu: `sudo raspi-config`
3. Change default password 
4. Change hostname to `1337-pi`
6. Interfacing Options > SSH > Yes, Enable SSH Server

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

Tip: use `windows+r` type `putty pi@1337-pi.local`

For my iPad I use [Termius](https://itunes.apple.com/us/app/termius-ssh-mosh-and-telnet-client/id549039908?mt=8)

## Set up Windows Sharing (SMB or Samba)

Back to [Table of Contents](#table-of-contents)

Full howto [here](https://raspberrypihq.com/how-to-share-a-folder-with-a-windows-computer-from-a-raspberry-pi/)

**In Short**:

1. `sudo apt-get install samba samba-common-bin`
2. `sudo nano /etc/samba/smb.conf`
3. Edit below, save and exit:
  * `wins support = yes` section `[global]` was `no`
  * `writeable = yes` section `[homes]` was `no`
4. `sudo smbpasswd -a pi` to create a smb password

On your Windows machine:

`net use u: \\1337-pi.local\pi /user:pi` or use the Windows GUI to map a share to a drive letter.

## Install Git

`sudo apt-get install git`

Configure your git instance:

  `git config --global user.email "you@example.com"`
  `git config --global user.name "Your Name"`

Todo: have git or ssh [remember my password](https://stackoverflow.com/questions/21956750/secure-push-and-pull-with-no-password-git)

Clone a git repo:
git clone

## Update your software

Back to [Table of Contents](#table-of-contents)

To make sure you are up to date with the software that we are going to install using `apt-get`. Next to that we need a proper editor, which is vim.

**In Short**:

1. `sudo apt-get update`
2. `sudo apt-get upgrade`

Optional: if you want vim instead of vi (skip if you use nano)

3. `sudo apt-get install vim`

## Backup and restore
Todo: how to backup and restore my pi

# WEBSERVER

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

todo: check if you have git installed!

1. `cd /home/pi`
2. `rm -rf *`
3. optional: `sudo apt-get install git`
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