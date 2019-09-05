# MOSIP Sandbox Deployment Guide

This document describes how to install MOSIP in an in-premise sandbox enviroment on a few virtual machines, capabable of running a pilot or a trial.

## Hardware prerequisites

Bare Metal or Virutal machines with following minimum configurations:  
**VM1**: CPU: 8 cores, RAM: 12GB, Storage: 512 GB  
**VM2**: CPU: 5 cores, RAM: 10GB, Storage: 512 GB   
**VM3**: CPU: 8 cores, RAM: 12GB, Storage: 512 GB   

## Operating System
CentOS 7

## Environment setup
All the components of environment to be installed on **VM1**.  
[Postgresql](#postgresql-db)  
[HDFS](#single-node-hdfs)  
[ClamAV](#clamav)  
[LDAP](#ldap)  
[Nginx](#nginx)  
[ActiveMQ](#activemq)

### Postgresql DB
* Install version 10.10 as per procedure given here: https://www.itzgeek.com/how-tos/linux/centos-how-tos/install-postgresql-9-3-on-centos-7.html

* Enable DB access from external machines by modifying `/var/lib/pgsql/10/data/postgresql.conf` and as below:  
```
listen_addresses = '*'
port= 5432  
unix_socket_directories = '/var/run/postgresql, /tmp'  
max_connections = 1000  
shared_buffers = 2GB
```
*  Add host entry in `/var/lib/pgsql/10/data/pg_hba.conf`as  
`host all all 0.0.0.0/0 md5`
*  Enable port 5432 on the firewall and restart:
```
$ firewall-cmd --zone=public --add-port=5432/tcp --permanent
$ firewall-cmd --reload
$ sudo systemctl restart postgres-10
``` 
### Single node HDFS
* Install as per procedure given here: https://www.tecmint.com/install-configure-apache-hadoop-centos-7/
* Download version 2.8.1 of Hadoop (the above procedure mentions older version).
* Create hdfs users with password as 'Mosip@dev123'
```
$ sudo useradd  regprocessor
$ sudo useradd  prereg
$ sudo useradd  idrepo
```
* Create directories for users:
```
$ hdfs dfs -mkdir /user/    
$ hdfs dfs -mkdir /user/regprocessor
$ hdfs dfs -chown -R regprocessor:regprocessor  /user/regprocessor
$ hdfs dfs -mkdir /user/prereg
$ hdfs dfs -chown -R prereg:prereg  /user/prereg
$ hdfs dfs -mkdir /user/idrepo
$ hdfs dfs -chown -R idrepo:idrepo  /user/idrepo
```
### ClamAV
* Install as per procedure give here: https://hostpresto.com/community/tutorials/how-to-install-clamav-on-centos-7/
* Enable firewall port 3310.
* Edit the follownig lines in `/usr/lib/systemd/system/freshclam.service`:  
```
[Unit]
Description = freshclam scanner
After = network.target

[Service]
Type = forking
ExecStart = /usr/bin/freshclam -d -c 4
Restart = on-failure
PrivateTmp = true
RestartSec = 20sec

[Install]
WantedBy=multi-user.target
```
* Restart  
`$ sudo systemctl restart freshclam`

### LDAP
* Download https://mirrors.estointernet.in/apache//directory/apacheds/dist/2.0.0.AM25/apacheds-2.0.0.AM25-64bit.bin
* Run the file:  
`$ bash apacheds-2.0.0.AM25-64bit.bin`  (accept all defaults)
* Enable firewall port 10389.

### Nginx
* https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7

### ActiveMQ
* https://www.vultr.com/docs/how-to-install-apache-activemq-on-centos-7

## Building MOSIP Services (Dockers)









