# mongoDB_opsManager

https://docs.opsmanager.mongodb.com/current/core/system-overview/
https://docs.opsmanager.mongodb.com/current/tutorial/install-simple-test-deployment/

```
$ sudo cat << EOF > /etc/yum.repos.d/mongodb-org-5.0.repo
[mongodb-org-5.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/5.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-5.0.asc
EOF

$ sudo yum install -y mongodb-org
$ sudo systemctl disable mongod
$ sudo mkdir -p /data/appdb
$ sudo chown -R mongod:mongod /data
$ sudo cat <<EOF > /etc/mongod_appdb.conf
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

$ sudo -u mongod mongod -f /etc/mongod_appdb.conf
$ sudo yum install -y net-tools
$ netstat -nltp |grep 27017
tcp        0      0 127.0.0.1:27017         0.0.0.0:*               LISTEN      -                   

$ sudo yum install -y wget
$ wget https://downloads.mongodb.com/on-prem-mms/rpm/mongodb-mms-5.0.3.100.20211005T2044Z-1.x86_64.rpm
$ sudo rpm -ivh mongodb-mms-5.0.3.100.20211005T2044Z-1.x86_64.rpm 

$ id mongodb-mms
uid=991(mongodb-mms) gid=988(mongodb-mms) groups=988(mongodb-mms)

$ sudo service mongodb-mms start
```
