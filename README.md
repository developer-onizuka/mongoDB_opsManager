# mongoDB_opsManager

https://docs.opsmanager.mongodb.com/current/core/system-overview/
https://docs.mongodb.com/manual/tutorial/install-mongodb-enterprise-on-red-hat/

```
$ sudo su
# cat << EOF > /etc/yum.repos.d/mongodb-enterprise-5.0.repo 
[mongodb-enterprise-5.0]
name=MongoDB Enterprise Repository
baseurl=https://repo.mongodb.com/yum/redhat/8/mongodb-enterprise/5.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-5.0.asc
EOF
# dnf install -y mongodb-enterprise

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

# sudo service mongodb-mms start
```
