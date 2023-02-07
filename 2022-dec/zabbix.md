# Zabbix 6.0 LTS Deployment for RHEL9

## Set your timezone to your appropriate location.
```
server~$ timedatectl set-timezone America/Winnipeg
```
## If SELinux status enabled in enforcing mode, you need to execute the following commands to enable communication between Zabbix frontend and server
```
server~$ # setsebool -P httpd_can_connect_zabbix on 
```
`If the database is accessible over network (including 'localhost' in case of PostgreSQL), you need to allow Zabbix frontend to connect to the database remotely.` 
```
server~$ setsebool -P httpd_can_network_connect_db on
```

## Update you RHEL system.
```
server~$ dnf update
server~$ dnf clean all
```
## Preparing the database that will be use by Zabbix 6.0 LTS, install mysql8.0 or higher.
```
server~$ dnf info mysql-server
server~$ dnf install mysql-server
```
## Enable and start the MySQL instance.
```
server~$ systemctl enable --now mysqld
```

## Securing the database with built-in security installation binary.
```
server~$ /usr/bin/mysql_secure_installation
```
## This will setup:
1. password
2. remove anonymous user
3. Disallow root login
4. remove built-in test database
5. Reload privilege table

## For MySQL tuning you can modify or add directive in the **`/etc/my.cnf.d/mysql-server.cnf`**.
`see tuning mysql for zabbix`

# Zabbix Installation Guide:
The following procedure below is taken from the official Zabbix Instalaltion Guide.

[**Zabbix Installation Guide**](https://www.zabbix.com/download?zabbix=6.2&os_distribution=red_hat_enterprise_linux&os_version=9&components=server_frontend_agent&db=mysql&ws=apache)

## Disable Zabbix packages provided by EPEL, if you have it installed. Edit file /etc/yum.repos.d/epel.repo and add the following statement.
```
[epel]
...
excludepkgs=zabbix*
```
## We will now install the zabbix repository.
```
server~$ rpm -Uvh https://repo.zabbix.com/zabbix/6.2/rhel/9/x86_64/zabbix-release-6.2-3.el9.noarch.rpm
```
## Then install the the zabbix server, frontend, and zabbix agent.
```
server~$ dnf install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent
```
## Next we will generate the database foe Zabbix instance.
```
server~$ mysql -uroot -p
password
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
```
## Since creating the database has without data and schema, we will need to import an existing schema and data to the newly created zabbix user identified by using `zabbix` password.
```
server~$ zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```
## Note: 
1. You will be prompted with a password, use the password that you have assigned to the zabbix user.
1. This will take a couple of minutes to generate.`

## Disable log_bin_trust_function_creators option after importing database schema.
```
server~$ mysql -uroot -p
password
mysql> set global log_bin_trust_function_creators = 0;
mysql> quit; 
```

## We will then add a password for the newly create database configuration for zabbix server from the imported schema. This password is should be the same as what the zabbix user has.
```
server~$ vi /etc/zabbix/zabbix_server.conf

DBPassword=password
```
## Finally we can now reload the Zabbix related services.
```
server~$ systemctl enable --now zabbix-server zabbix-agent httpd php-fpm
```
`Note: You can also check the logs for errors or issues related to zabbix `**/var/log/zabbix/zabbix_server.log**`.

## In your browser: 
```
http://ip-address/zabbix
```
These is to continue the final setup of the Zabbix server.
```
Default Login: Admin

Default Password: zabbix
```

# Zabbix Setup Work Flow
`Prerequisites:`
* Setup your DNS and DHCP server
* Install zabbix-agent on all hosts to be monitored by the zabbix server.
* Configure the zabbix agent config file to point to the zabbix server.
`/etc/zabbix/zabbix_agentd.conf`

## Note: You can also do this using ansible configuration management if there are multiple hosts to be monitored. 

## Procedures:
1. Set the map according to your location.
1. Setup admin user.
1. Configuration -> Discovery -> Set the local subnet and Enable it. Checks -> Add -> check type = icmp ping, http, ssh. Update Interval = 5m. Click Add and Update.
1. Action -> Discovery Actions -> Create Action.
1. In the Create Action ->  Name: Add Host in LAN Automatically, In Conditions -> Add Type = Discovery Rule and click Add.
1. Operations -> Add -> Operation Detail = Add Host and Click Add.
1. Monitoring -> Discovery (Check the newly discovered End points/Servers.)
1. Monitoring -> Hosts -> Click any Host -> Configuration -> Input Hostname, Visible Name, Template, Host Group, Enable it -> Click Update.

## Sending email Norification:

1. Administration -> Media Type -> Type = Email(HTML) [setup your smtp connection]
1. Users -> Admin -> Media -> Media Add -> Type = Email(HTML) -> Click Add
1. Configurations -> Actions -> Trigger Actions -> Report Problems to Zabbix Adminsitrators -> Conditions Add -> Type = Trigger Severity, Operator = Is greater than or equals, Severity = High -> Click Add. 