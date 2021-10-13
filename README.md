# mongoDB_opsManager

https://docs.opsmanager.mongodb.com/current/core/system-overview/

https://docs.opsmanager.mongodb.com/current/tutorial/install-simple-test-deployment/

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


If you find a result like below, then you might remove the /tmp/mongodb-27017.sock so that you could restart it again.
```
# systemctl start mongod.service
Job for mongod.service failed because the control process exited with error code.
See "systemctl status mongod.service" and "journalctl -xe" for details.
```
