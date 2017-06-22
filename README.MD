# Introduction
These are my notes to install a full blown mean stack (MongoDB, Express, Angular4, Node) on the pi, including git, some build and deploy tool. I keep these notes to be able to quickly set up, or reinstall my environment. 

# Table of Contents
1. [Setup](#setup)
2. [Sources](#sources)


Probably I'm still working on it, but this is how far I came, or care to write down.

## Setup
Back to [Table of Contents](#table-of-contents)

* Raspberry pi - ehm... <sup>[1](http://google.nl "hover text")</sup>  

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
![alt text][prof] 
![alt text][bus]

[bus]: "data:image/gif;base64,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"

display(HTML('''<img src="data:image/gif;base64,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">'''))

* [prof]: (http://data:image/png;base64,PGh0bWwgbGFuZz0ibmwiPjxoZWFkPjxtZXRhIGNvbnRlbnQ9InRleHQvaHRtbDsgY2hhcnNldD1VVEYtOCIgaHR0cC1lcXVpdj0iY29udGVudC10eXBlIj48dGl0bGU+S2VubmlzZ2V2aW5nIHZvb3Igb21sZWlkaW5nPC90aXRsZT48c3R5bGU+Ym9keSxkaXYsYXtmb250LWZhbWlseTphcmlhbCxzYW5zLXNlcmlmfWJvZHl7YmFja2dyb3VuZC1jb2xvcjojZmZmO21hcmdpbi10b3A6M3B4fWRpdntjb2xvcjojMDAwfWE6bGlua3tjb2xvcjojMDBjfWE6dmlzaXRlZHtjb2xvcjojNTUxYThifWE6YWN0aXZle2NvbG9yOnJlZH1kaXYuX2xGZXtib3JkZXItdG9wOjFweCBzb2xpZCAjYmJiO2JvcmRlci1ib3R0b206MXB4IHNvbGlkICNiYmI7YmFja2dyb3VuZDojZjJmMmYyO21hcmdpbi10b3A6MWVtO3dpZHRoOjEwMCV9ZGl2Ll9rRmV7cGFkZGluZzowLjVlbSAwO21hcmdpbi1sZWZ0OjEwcHh9ZGl2Ll9qRmV7bWFyZ2luLWxlZnQ6MzVweDttYXJnaW4tdG9wOjM1cHh9PC9zdHlsZT48c2NyaXB0PmZ1bmN0aW9uIGdvX2JhY2soKXt3aW5kb3cuaGlzdG9yeS5nbygtMSk7cmV0dXJuIGZhbHNlO30KCmZ1bmN0aW9uIGN0dShvaSxjdCl7dmFyIGxpbmsgPSBkb2N1bWVudCAmJiBkb2N1bWVudC5yZWZlcnJlcjt2YXIgZXNjX2xpbmsgPSAiIjt2YXIgZSA9IHdpbmRvdyAmJiB3aW5kb3cuZW5jb2RlVVJJQ29tcG9uZW50ID9lbmNvZGVVUklDb21wb25lbnQgOmVzY2FwZTtpZiAobGluayl7ZXNjX2xpbmsgPSBlKGxpbmspO30KbmV3IEltYWdlKCkuc3JjID0gIi91cmw/c2E9VCZ1cmw9IiArIGVzY19saW5rICsgIiZvaT0iICsgZShvaSkrICImY3Q9IiArIGUoY3QpO3JldHVybiBmYWxzZTt9Cjwvc2NyaXB0PjwvaGVhZD48Ym9keT48ZGl2IGNsYXNzPSJfbEZlIj48ZGl2IGNsYXNzPSJfa0ZlIj48Zm9udCBzdHlsZT0iZm9udC1zaXplOmxhcmdlciI+PGI+S2VubmlzZ2V2aW5nIHZvb3Igb21sZWlkaW5nPC9iPjwvZm9udD48L2Rpdj48L2Rpdj48ZGl2IGNsYXNzPSJfakZlIj4mbmJzcDtEZSBwYWdpbmEgd2Fhcm9wIHUgemljaCBiZXZpbmR0LCBwcm9iZWVydCB1IG5hYXIgPGEgaHJlZj0iaHR0cDovL2ZpbGVzLnNvZnRpY29ucy5jb20vZG93bmxvYWQvdHYtbW92aWUtaWNvbnMvZnV0dXJhbWEtaWNvbnMtYnktcmljaC1kL3BuZy8xMjgvUHJvZmVzc29yLnBuZyI+aHR0cDovL2ZpbGVzLnNvZnRpY29ucy5jb20vZG93bmxvYWQvdHYtbW92aWUtaWNvbnMvZnV0dXJhbWEtaWNvbnMtYnktcmljaC1kL3BuZy8xMjgvUHJvZmVzc29yLnBuZzwvYT4gdGUgc3R1cmVuLjxicj48YnI+Jm5ic3A7QWxzIHUgZGUgYmV0cmVmZmVuZGUgcGFnaW5hIG5pZXQgd2lsdCBiZXpvZWtlbiwga3VudCB1IDxhIGhyZWY9IiMiIG9uY2xpY2s9InJldHVybiBnb19iYWNrKCk7IiBvbm1vdXNlZG93bj0iY3R1KCd1bmF1dGhvcml6ZWRyZWRpcmVjdCcsJ29yaWdpbmxpbmsnKTsiPnRlcnVnZ2FhbiBuYWFyIGRlIHZvcmlnZSBwYWdpbmE8L2E+Ljxicj48YnI+PGJyPjwvZGl2PjwvYm9keT48L2h0bWw+)