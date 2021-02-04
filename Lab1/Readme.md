# Running OAC on MDS
Oracle Analytics Cloud (OAC) is an enterprise analytics solution for building analytics dashboard, pixel-perfect high quality reports. OAC provides Machine Learning algorithm to exploit insights from your data

In order to build analytics dashboard using OAC on MDS HeatWave, we need to do the following
1. Provision a bastion host to connect to MDS/HeatWave
2. Create a user account on MDS
3. Create a MDS connection on OAC
4. Starting building your dashboard

## Provision a bastion host
1. Provision a compute instance on OCI with a Public IP address
2. ssh to the compute instance using **opc** user with the private key you created in step 1
3. Install mysql clients
```
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
sudo yum repolist all | grep mysql
sudo yum-config-manager --enable mysql80-community
sudo yum repolist enabled | grep mysql
sudo yum install mysql-client mysql-shell mysql-router
```

4. Locate your MDS hostname to create an account
```
mysql -uadmin -hyour_mds_host_name -P3306 -p
create user oac_user@'%' identified with mysql_native_password by 'oac_secret';
grant all privileges on *.* to oac@'%';
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
![oac-12](img/oac-12.png)

2. Select the OAC instance you provisioned to access the OAC console by clicking on **Analytics Home Page** 
![oac-13](img/oac-13.png)

3. First of all, we need to create a **Connection** to HeatWave to build a dashboard

![oac-14](img/oac-14.png)

4. Next we are ready to build the dashboard on MDS HeatWave


## [Optional] Additional database import to MDS

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

