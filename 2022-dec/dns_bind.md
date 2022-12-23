# DNS - BIND9 package

`DNS` or `NameServer` is use to translate hostnames to IP address and thus it is the foundation of the internet and other network services. There are two files that are critical when implementing dns service. These are `forward` and `reverse` zone file and are managed inside the `named.conf` file.


## Installing, Querying and Backing Up the BIND9
``` 
#yum install bind bind-utils
```
```
#rpm -qa | grep -i bind
```
```
#cp -p /etc/named.conf /etc/named.conf.bak
```

## Modifying the main configuration file 
```
#vi etc/named.conf

	listen-on port 53 {127.0.0.1; 192.168.214.148; }; ###DNS SERVER IP###
	allow-query {localhost; 192.168.214.0/24; }; ###IP RANGE###
	allow-transfer {localhost; 192.168.214.147; }; ###SLAVE DNS IP###
	
	recursion yes;


	zone "rusami.org" IN {
	type master;
	file "fwd.rusami.org.db";
	allow-update { none; };
	};

	zone "214.168.192.in-addr.arpa" IN {
	type master;
	file "214.168.192.db";
	allow-update { none; };
	};
```
## Enabling and Starting the BIND service
```
#systemctl enable --now named
#systemctl status named
```
## Allow port for client DNS request
```
#firewall-cmd --permanent --add-port=53/udp
#firewall-cmd --permanent --add-port=53/tcp
OR
#firewall-cmd --get-services
#firewall-cmd --permanent --zone=public --add-service=dns
#firewall-cmd --list-all
#firewall-cmd --reload
```

## Create a forward zone file (fwd.rusami.org.db)
```
#cd /var/named
```
```
#vi /var/named/fwd.rusami.org.db
```
```
$TTL 3D
@	IN	SOA	dns.rusami.org.	root.rusami.org. (
2019040701	;Serial
3600		;Refresh
1800		;Retry
604800		;Expire
86400		;Minimum TTL
)
;Name Server Information
@	IN	NS	dns.rusami.org.
@	IN	NS	ws01.rusami.org.
@	IN	MX	5  mail.rusami.org.
;IP address of Name Server
dns	IN	A	192.168.214.148
;Mail Exchanger Information
;A - Record HostNmae To IP Address
ws01	IN	A	192.168.214.149
;CNAME	record
ftp	IN	CNAME	ws01.rusami.org.
```	

## Create a reverse zone file (214.168.192.db)
```
#vi /var/named/214.168.192.db
```
```
$TTL 86400
@	IN	SOA	dns.rusami.org.	root.rusami.org. (
2019040701	;Serial
3600		;Refresh
1800		;Retry
604800		;Expire
86400		;Minimum TTL
)
;Name Server Information
@	IN	NS	dns.rusami.org.
@	IN	NS	ws01.rusami.org.
@	IN	MX	5  mail.rusami.org.
;Reverse lookup for Name Server
148	IN	PTR	dns.rusami.org.
;PTR Record IP address to HostName
149	IN	PTR	ws01.rusami.org.
```
## Restart the BIND service again
```
#systemctl restart named
```

## Permission,Ownership and SElinux
```
#chgrp named -R /var/named
#chown -v root:named /etc/named.conf
#restorecon -rv /var/named
#restorecon /etc/named.conf
```

## Test DNS config for error:
```
#named-checkconf /etc/named.conf

#named-checkzone rusami.org /var/named/fwd.rusami.org.db

#named-checkzone rusami.org /var/named/214.168.192.db
```

## For the workstation/client, use can use `nmtui` or edit the `resolve.conf` file. In this case we will edit the `resolv.conf` file.
```
#vi /etc/resolv.conf
```
```
nameserver 192.168.214.148
```
## Restart the NetworkManager
```
#systemctl restart NetworkManager
```

`Note: Don't forget to remove the entry in your `/etc/host` file`

## Now you can verify if it's working
```
#nslookup ws01.rusami.org
#nslookup dns.rusami.org
#ping dns.rusami.org
#dig www.redhat.com
```

[ Bind9 Documentation ](https://www.isc.org/bind/)