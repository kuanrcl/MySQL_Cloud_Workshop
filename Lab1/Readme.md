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

1. First of all, create a policy to provision OAC instance
2. Click on the hamburger on the top left corner of OCI console, navigate to Identify -> Policies
3. Specify a policy name such as **oac-policy**, enter the folloing policy in the policy editor by clicking on **customized**
```
allow group Administrator-Group to manage analytics-instances in tenancy
allow group Administrator-Group to manage analytics-instances in compartment MyOracleAnalytics
```

![policy](img/policy.png)

4. Once you created the policy, click of the hamburger on the top left corner of OCI console, navigate to **Analytics-> Analytics Clouds**
![oac-11](img/oac-11.png)

5. Specify the name of the OAC instance such as **myoac**, just leave all the default options, and click on **Create**
![oac-10](img/oac-10.png)

# Build Dashboard on OAC

1. Navigate to **hamburger->Analytics->Analytics Clouds**
2. Select the OAC instance you provisioned to access the OAC console
![oac-12](img/oac-12.png)

