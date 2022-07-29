---
title: "How to setup log rotate in Linux to avoid running out of the disk space 👋"
description: "Setting up log rotate and monitoring the files is mandatory to avoid running out of the disk space. Log rotate is a linux service that helps us to safeguard from running out of the disk space." 
lead: "Logs from web servers and many different software applications are the reason why we run out of the disk space. When we deploy software applications having an observability layer is crucial to understand what is happening in the application but at an expense of disk storage. In this article we explore the ways log rotate can help us is rotating the logs we won't run out of the disk space."
date: 2022-07-27T09:19:42+01:00
lastmod: 2022-07-27T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
type: "docs"
url: "/log-rotate-config-in-linux-to-avoid-running-out-of-space/"
keywords:
  - log rotate
  - disk space management
  - disk space
  - linux service
  - systemctl
tags: ['log rotate', 'disk space management']
categories: ["Linux tips"]

---

## Description

Most of the linux services generate logs unless we configured it in a way that it won't generate logs forever. That is not an ideal solution with any of the production systems. Without rotation there is a high chance the system will run out of space. The command to check the size of the files in Linux is <code>ls -lh</code>. All the linux service logs are stored in the <code>/var/log/</code> folder space. Now let's see how log rotate can help to rotate the logs.

## What does the logrotate do?

Logrotate helps us to manage logs and not occupy a huge disk space by user-defined configs. It will just store all the historical logs for a prescribed time period and then delete it. All the old archive logs will be deleted based on the config. We could modify the config at any time based on our prediction on how quick logs may get accumulated from a Linux service. Since most of the logs are stored in the <code>/var/log/</code> we could use the command <code>du -h /var/log/ | sort -hr
</code> to see the size of the whole directory and also individual files. 

<center>
<img src="du-h.PNG" height=500px width=700px alt="Check the size of directory"/>
</center>

Any Linux machine that hosts a web server will generate lots of logs because of the connection requests it recieves from the outer world. Log rotate has many functionalities that helps us to manage these logs in an efficient manner.

## Installing Logrotate

Many Linux distros comes with an installed version of Logrotate and services that generated lots of log files. First thing to do is to check whether logrotate is available in the Linux version or not using the below command: <code>which logrotate </code>.

If there is no logrotate available in the system then install it using the below command in any version of Ubuntu:

<code>sudo apt-get install logrotate</code>

Use Yum incase of CentOS, brew incase of MAC OS, etc. 

## How Logrotate works?

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

/var/log/nginx/*.log {
        weekly
        missingok
        rotate 52
        compress
        delaycompress
        notifempty
        create 0640 www-data adm
        sharedscripts
        prerotate
                if [ -d /etc/logrotate.d/httpd-prerotate ]; then 
                        run-parts /etc/logrotate.d/httpd-prerotate; 
                fi 
        endscript
        postrotate
                invoke-rc.d nginx rotate >/dev/null 2>&1
        endscript
}

{{</highlight >}}

In the above example, the first line tells the logrotate service which log file to look for and apply this configuration. <i>*</i> represents all the files with the extension log. Now let us closely look into all the configuration parameters and find what functionality it provides us.

## Rotation interval

The rotation interval defines how often logrotate wants to rotate the logs. Logrotate has pre-defined set of values: <code>daily</code>,<code>weekly</code>, <code>monthly</code>,<code>yearly</code>. If the rotation interval is not set then the logs are rotated whenever logrotate runs if the time interval is also not in the configuration. 

We could set the time interval by creating a file in <code>/etc/cron.hourly</code>. Not much detail here as this is a never ending topic to dig in and learn.



<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>
