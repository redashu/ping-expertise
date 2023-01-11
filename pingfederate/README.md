## How to setup stand alone ping server in amazon linux 

### creating VM / baremetal -- with below given minimum configuration 

```

Minimum hardware requirements
Multi-core Intel Xeon processor or higher
4 CPU/Cores recommended

4 GB of RAM
1.5 GB available to PingFederate

1 GB of available hard drive space
```

## Steps to perform 

### step 1 -- Installing jdk 8 , 11 , 17 

```
 sudo yum install java-11-amazon-corretto -y
 sudo yum install java-11-amazon-corretto-devel
 sudo yum install java-11-amazon-corretto-jmods
 sudo yum install java-11-amazon-corretto-devel
```

### setting java_home with root or any other user -using the one you want to run ping federate server

```
[root@ip-172-31-82-223 ~]# cat /root/.bashrc 
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Source global definitions
if [ -f /etc/bashrc ]; then
	. /etc/bashrc
fi

JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
PATH=$JAVA_HOME/bin:$PATH
export PATH

```

### step 2 _- create account on ping portal and download Ping-federate zip and license 

```
[root@ip-172-31-82-223 ~]# cd /opt/
[root@ip-172-31-82-223 opt]# ls
aws  pingfederate-11.2.0  pingfederate-11.2.0.zip
```

### setting up pingfederate.service 

```
[root@ip-172-31-82-223 linux]# pwd
/opt/pingfederate-11.2.0/pingfederate/sbin/linux
[root@ip-172-31-82-223 linux]# ls
pingfederate  pingfederate.service
[root@ip-172-31-82-223 linux]# cat pingfederate.service 
[Unit]
Description=PingFederate ${PF_VERSION}
Documentation=https://support.pingidentity.com/s/PingFederate-help

[Install]
WantedBy=multi-user.target

[Service]
Type=simple
User=ping-user
WorkingDirectory=${PF_HOME}
Environment='JAVA_HOME=${PF_JAVA_HOME}'
ExecStart=${PF_HOME}/bin/run.sh
ExecStop=${PF_HOME}/sbin/pingfederate-shutdown.sh
```

### step 3 : copy this to systemd location and change configuration 

```
[root@ip-172-31-82-223 linux]# cat /etc/systemd/system/pingfederate.service 
[Unit]
Description=PingFederate ${PF_VERSION}
Documentation=https://support.pingidentity.com/s/PingFederate-help

[Install]
WantedBy=multi-user.target

[Service]
Type=simple
User=ping-user
WorkingDirectory=/opt/pingfederate-11.2.0/pingfederate/
Environment='JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64/'
ExecStart=/opt/pingfederate-11.2.0/pingfederate/bin/run.sh
ExecStop=/opt/pingfederate-11.2.0/pingfederate/sbin/pingfederate-shutdown.sh
```

### starting service and checking ports 

```
[root@ip-172-31-82-223 linux]# systemctl status pingfederate
● pingfederate.service - PingFederate ${PF_VERSION}
   Loaded: loaded (/etc/systemd/system/pingfederate.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2023-01-10 04:16:23 UTC; 1 day 1h ago
     Docs: https://support.pingidentity.com/s/PingFederate-help
 Main PID: 7076 (bash)
   CGroup: /system.slice/pingfederate.service
           ├─7076 bash /opt/pingfederate-11.2.0/pingfederate/bin/run.sh
           └─7130 /usr/lib/jvm/java-11-amazon-corretto.x86_64//bin/java -Xms512m -Xmx1024m -XX:+UseG1GC -Dprogram.name=run.sh -Djava.security.egd=file:/dev/...

Jan 10 04:16:23 ip-172-31-82-223.ec2.internal systemd[1]: Started PingFederate ${PF_VERSION}.
Jan 10 04:16:40 ip-172-31-82-223.ec2.internal run.sh[7076]: PingFederate running...
[root@ip-172-31-82-223 linux]# 
[root@ip-172-31-82-223 linux]# netstat -nlpt 
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:9031            0.0.0.0:*               LISTEN      7130/java           
tcp        0      0 0.0.0.0:9999            0.0.0.0:*               LISTEN      7130/java  
```

### step 4: Now you can open pingserver in web browser -this will ask for license and username password to do complete setup

https://ip:9999

## PingFederate COnfiguration file 

```
[root@ip-172-31-82-223 bin]# pwd
/opt/pingfederate-11.2.0/pingfederate/bin
 run.properties
```



