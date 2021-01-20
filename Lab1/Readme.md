# Running OAC on MDS

## Import Database

1. Gather MDS information
* MDS host name
* user
* password

2. Install mysql client
```
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
sudo yum install mysql mysql-router 
```
3. Configure mysqlrouter
```
sudo cat << EOF >> /etc/mysqlrouter/mysqlrouter.conf
[routing:forward]
bind_address = 0.0.0.0
bind_port = 6446
destinations = 10.0.1.37:3306
routing_strategy = round-robin
EOF
mysql -uadmin -h127.0.0.1 -P6446 -p
```

4. Create sample **employee** database in MDS
```
mkdir data
cd data
wget https://github.com/datacharmer/test_db/archive/master.zip
unzip master.zip
cd test_db-master
mysql -uadmin -h<mds-hostname> -P3306 -p < employees.sql
```

## Provision OAC instance
