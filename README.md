# mongoDB_opsManager

https://docs.opsmanager.mongodb.com/current/core/system-overview/

https://docs.opsmanager.mongodb.com/current/tutorial/install-simple-test-deployment/

# 0. Install CentOS 8 as Virtual Machine
```
$ git clone https://github.com/developer-onizuka/mongoDB_opsManager.git
$ cd mongoDB_opsManager
$ vagrant up --provider=libvirt
$ ssh vagrant@192.168.133.12
```

# 1. Install mongodb-org
```
$ sudo su
# cat << EOF > /etc/yum.repos.d/mongodb-org-5.0.repo  
[mongodb-org-5.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/5.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-5.0.asc
EOF
# dnf install -y mongodb-org
```

# 2. Creating directory for Ops Manager App DB
```
# sudo mkdir -p /data/appdb
# sudo chown -R mongod:mongod /data

# cat <<EOF > /etc/mongod_appdb.conf
systemLog:
  destination: file
  path: "/data/appdb/mongodb.log"
  logAppend: true
storage:
  dbPath: "/data/appdb"
  journal:
    enabled: true
  wiredTiger:
    engineConfig:
      cacheSizeGB: 1
processManagement:
  fork: true
  timeZoneInfo: /usr/share/zoneinfo
  pidFilePath: /var/run/mongodb/mongod.pid
net:
  bindIp: 127.0.0.1
  port: 27017
setParameter:
  enableLocalhostAuthBypass: false
EOF
# sudo -u mongod mongod -f /etc/mongod_appdb.conf
# dnf install -y net-tools
# netstat -nltp |grep 27017
tcp        0      0 127.0.0.1:27017         0.0.0.0:*               LISTEN      -                   

# dnf install -y wget
# wget https://downloads.mongodb.com/on-prem-mms/rpm/mongodb-mms-5.0.3.100.20211005T2044Z-1.x86_64.rpm
# sudo rpm -ivh mongodb-mms-5.0.3.100.20211005T2044Z-1.x86_64.rpm 

# id mongodb-mms
uid=991(mongodb-mms) gid=988(mongodb-mms) groups=988(mongodb-mms)

# reboot
```

If you find a result like below, then you might remove the /tmp/mongodb-27017.sock so that you could restart it again.
```
# systemctl start mongod.service
Job for mongod.service failed because the control process exited with error code.
See "systemctl status mongod.service" and "journalctl -xe" for details.
```


# 3. Start MongoDB Monitoring Service (MMS)

You should reboot before process. If don't do that, the mongodb-mms will stuck.
```
# sudo service mongodb-mms start
Starting pre-flight checks
Successfully finished pre-flight checks

Migrate Ops Manager data
   Running migrations...                                   [  OK  ]
Starting Ops Manager server
   Instance 0 starting................                     [  OK  ]
Starting pre-flight checks
Successfully finished pre-flight checks

Start Backup Daemon...                                     [  OK  ]

# netstat -nltp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:27017         0.0.0.0:*               LISTEN      -                   
tcp6       0      0 :::111                  :::*                    LISTEN      -                   
tcp6       0      0 :::8080                 :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      - 
```

# 4. mongoDB-mms-agent startup daemon

You can get the following information from the mongoDB-mms-server when deproying replicaSet. 
```
$ curl -OL http://192.168.33.12:8080/download/agent/automation/mongodb-mms-automation-agent-manager-11.0.8.7002-1.x86_64.rhel7.rpm
$ sudo rpm -U mongodb-mms-automation-agent-manager-11.0.8.7002-1.x86_64.rhel7.rpm
$ sudo vi /etc/mongodb-mms/automation-agent.config
$ sudo mkdir -p /data
$ sudo chown mongod:mongod /data
$ sudo systemctl start mongodb-mms-automation-agent.service
$ sudo /sbin/service mongodb-mms-automation-agent start
```

But you might set up the agent daemon should be enabled thru systemctl command, like below:
```
[vagrant@mongo-0 ~]$ sudo systemctl enable mongodb-mms-automation-agent.service
Created symlink /etc/systemd/system/multi-user.target.wants/mongodb-mms-automation-agent.service → /etc/systemd/system/mongodb-mms-automation-agent.service.

[vagrant@mongo-0 ~]$ sudo systemctl status mongodb-mms-automation-agent.service
● mongodb-mms-automation-agent.service - MongoDB MMS Automation Agent
   Loaded: loaded (/etc/systemd/system/mongodb-mms-automation-agent.service; enabled; vendor preset: disa>
   Active: active (running) since Sun 2021-10-17 03:46:55 UTC; 51s ago
  Process: 697 ExecStartPre=/usr/bin/chown -R mongod:mongod /var/run/mongodb-mms-automation (code=exited,>
  Process: 669 ExecStartPre=/usr/bin/mkdir /var/run/mongodb-mms-automation (code=exited, status=0/SUCCESS)
 Main PID: 706 (sh)
   Memory: 119.7M
   CGroup: /system.slice/mongodb-mms-automation-agent.service
           ├─706 /bin/sh -c /opt/mongodb-mms-automation/bin/mongodb-mms-automation-agent -f /etc/mongodb->
           └─710 /opt/mongodb-mms-automation/bin/mongodb-mms-automation-agent -f /etc/mongodb-mms/automat>

Oct 17 03:46:55 mongo-0 systemd[1]: Starting MongoDB MMS Automation Agent...
Oct 17 03:46:55 mongo-0 systemd[1]: Started MongoDB MMS Automation Agent.
```
