---
layout: post
title:  "Project KENS/KNS"
date:   2019-08-26 09:00:00
categories: Python
permalink: /archivers/python-project-kens
nocomments: false
use_math: true 
---

# Project KENS

This is a web-scraping project for Konkuk University pages. KNS is a sub-project which was not planned to be made. KNS scraps the main homepage of Konkuk University, where as KENS scraps Electronic Engineering homepage in particular.

## Install

This project was designed for environment of home-server, especially Raspberry-Pi. KENS is a simple mini project, thus it does not provide any special functions, since it does not run on 24-hour running server, but runs by cron scheduler. This section explains how to install the project.

<!--more-->

First download the project to **Desktop/git**

```bash
pi@raspberrypi:~ $ cd Desktop
pi@raspberrypi:~ $ git clone https://github.com/dev-acoustikue/project_kens/
```

Make directory below Desktop folder, naming as project_kens_log. This is where log files will be stored. This is highly suggested.

```bash
pi@raspberrypi:~ $ cd Desktop
pi@raspberrypi:~ $ mkdir project_kens_log
```

Now, logs named in the form of 'kens-[year]-[Month]-[Date]-[Hour]-[Minute]' or 'kns-[year]-[Month]-[Date]-[Hour]-[Minute]' will be piled up here.



## Settings

Only some script files need to be registered in crontab, not every scripts. The scripts are, **/Rpi/KensRunRpi.py** and **/Rpi/KnsRunRpi.py** .

Open crontab to set those files like below. Doing this by superuser account is highly suggested.

```bash
pi@raspberrypi:~ $ sudo su
root@raspberrypi:~# crontab -e
```

And add the script to run. This is an example of running scripts in every 30 minutes. Set if you like to write logs. In this example, actions of removing logs at a specific time every day was added.

```
# added 19.08.25.
0 * * * * /usr/bin/python3 /home/pi/Desktop/git/project_kens/Rpi/KensRunRpi.py > /home/pi/Desktop/project_kens_log/kens-`date +\%Y-\%m-\%d-\%H-\%M`
2 * * * * /usr/bin/python3 /home/pi/Desktop/git/project_kens/Rpi/KnsRunRpi.py > /home/pi/Desktop/project_kens_log/kns-`date +\%Y-\%m-\%d-\%H-\%M`

30 * * * * /usr/bin/python3 /home/pi/Desktop/git/project_kens/Rpi/KensRunRpi.py > /home/pi/Desktop/project_kens_log/kens-`date +\%Y-\%m-\%d-\%H-\%M`
32 * * * * /usr/bin/python3 /home/pi/Desktop/git/project_kens/Rpi/KnsRunRpi.py > /home/pi/Desktop/project_kens_log/kns-`date +\%Y-\%m-\%d-\%H-\%M`

0 5 * * * rm /home/pi/Desktop/project_kens_log/*

```

Now run the cron service.

```bash
root@raspberrypi:~# service cron start
```

To check the service whether it is running,

```bash
root@raspberrypi:~# service cron status
```


If you want to stop the script, then write the command like below.

```bash
root@raspberrypi:~# service cron stop
```

Examine if the logs are piling up.







