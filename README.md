# mongoDB_opsManager

https://docs.opsmanager.mongodb.com/current/core/system-overview/
https://docs.opsmanager.mongodb.com/current/tutorial/install-simple-test-deployment/

```
$ sudo su
# cat << EOF > /etc/yum.repos.d/mongodb-org-4.4.repo
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
EOF

# dnf install -y mongodb-org
# systemctl disable mongod
# sudo mkdir -p /data/appdb
# sudo chown -R mongod:mongod /data
$ cat <<EOF > /etc/mongod_appdb.conf
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
# wget https://downloads.mongodb.com/on-prem-mms/rpm/mongodb-mms-4.4.17.100.20210901T1617Z-1.x86_64.rpm
# sudo rpm -ivh mongodb-mms-4.4.17.100.20210901T1617Z-1.x86_64.rpm 

# id mongodb-mms
uid=991(mongodb-mms) gid=988(mongodb-mms) groups=988(mongodb-mms)

# sudo service mongodb-mms start
```
