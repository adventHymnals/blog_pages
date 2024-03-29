---
layout: post
current: post
navigation: True
title: Setting up a Csycms Server for Hosting Advent Hymnals
date: 1908-01-01 09:00:00
tags: [technical, server]
class: post-template
subclass: 'post'
author: brian
---


[Csycms](https://github.com/csymapp/csycms-cli) is a flat file content management system for nodejs which we developed about 4 to 5 years back. It has served us well hosting both the sites for [Advent Hymnals](https://adventhymnals.org) as well as the [Sabbath School Lessons Archive](https://sabbathschool.github.io/) before we moved it to github pages. And now we have to retire it. While we could keep improving it to keep up with our current needs, that will be time and resources not wisely spent. We have now fully adopted github pages as our host for advent hymnals, and mainly because it is free. But as we work on the migration, there may be need to migrate our csycms system from one server to another. So here are the instructions incase this need should arise.

## Requirements
1. An ubuntu server
2. Node.js >=14.0.0 <18.0.0
3. Nginx. You can use Apache or any other webserver of your choice. But we only have configurations for nginx.
4. certbot

One you have set up these requirements, you are good to go. If you are not sure how to go about it you can check out the instructions elsewhere in the internet.

## Installation
First install csycms: 
```bash
npm install -g csycms
```

or 

```bash
sudo npm install -g csycms --unsafe-perm
```

Then initialize it and create service files:
```bash
sudo csycms init
```

Then install advent hymnals site:
```bash
csycms site --create -n adventhymnals -p 8710 -r https://github.com/GospelSounders/adventhymnals.git -d adventhymnals.org
```

or 

```bash
sudo csycms site --create -n adventhymnals -p 8710 -r https://github.com/GospelSounders/adventhymnals.git -d adventhymnals.org
```

Then make changes to configurations in `/etc/csycms/sites-enabled/adventhymnals.yml`. A working configuration is found [here](https://github.com/adventHymnals/resources/blob/master/configurations/adventhymnals.yml)
```
sudo nano /etc/csycms/sites-enabled/adventhymnals.yml
```

Be sure to change:
- domain. Assets will not be loaded if this is wrong
- scheme. If this is wrong, assets will not be loaded properly.
- site.space
- site.title
- copyright.title
- copyright.url
- copyright.name

Now its time to setup nginx to act as a proxy to our csycms server. Sample configuration is found [here](https://github.com/adventHymnals/resources/blob/master/configurations/adventhymnals-nginxconfiguration). You can have it either an independent file in the nginx config directory or set it up as a block in the main config. Since it redirects all traffic to ssl, you will need to get some ssl certificates. You will use certbot for this.

```bash
sudo certbot certonly --nginx -d adventhymnals.org
```

Install csycms GS theme

```bash
sudo csycms theme pull GS
```

Now restart both csycms and nginx:

```bash
sudo systemctl restart nginx
sudo systemctl restart csycms
```
