
# Keepalived
August 31, 2022 by RB

Load balancing is a method of distributing IP traffic across a cluster of real servers, providing one or more highly available virtual services. When designing load-balanced topologies, it is important to account for the availability of the load balancer itself as well as the real servers behind it.

keepalived provides frameworks for both load balancing and high availability. The load balancing framework relies on the well-known and widely used Linux Virtual Server (IPVS) kernel module, which provides Layer 4 load balancing. High availability is achieve using VRRP.

According to IEEE RFC 3768 Virtual Router Redundancy Protocol (VRRP) specifies an election protocol that dynamically assigns responsibility for a virtual router to one of the VRRP routers on a LAN. The VRRP router controlling the IP address(es) associated with a virtual router is called the Master and forwards packets sent to these IP addresses. The election process provides dynamic failover in the forwarding responsibility should the Master become unavailable. This allows any of the virtual router IP addresses on the LAN to be used as the default first hop router by end-hosts. The advantage gained from using VRRP is a higher availability default path without requiring the configuration of dynamic routing or route discovery protocols on every end-host.

Before we begin, we need to install keepalived but first off, installing it out of your package repo is not a good idea as I bet they are out of date. As of this writing ubuntu's repository has version 2.0.19 which is dated 2019 and the latest stable release is version 2.2.7 which was released in January 2022. So it might be a good idea to download it from the source.

## Installation prerequisites on Debian:
```
$ sudo apt-get install curl gcc libssl-dev libnl-3-dev libnl-genl-3-dev libsnmp-dev
```

## Build and Install
```
$ curl --progress http://keepalived.org/software/keepalived-2.2.7.tar.gz | tar xz
$ cd keepalived-2.2.7
$ ./configure --prefix=/usr/local/keepalived-2.2.7
$ make
$ sudo make install
```

This makes it easy to uninstall a compiled version of keepalived simply by deleting the parent directory. Additionally, this method of installation allows for multiple versions of Keepalived installed without overwriting each other. Use a symlink to point to the desired version. For example, your directory layout could look like this:
```
[root@server1 ~]# cd /usr/local
[root@server1 local]# ls -l
total 12
lrwxrwxrwx. 1 root root   17 Feb 24 20:23 keepalived -> keepalived-2.2.7
drwxr-xr-x. 2 root root 4096 Feb 24 20:22 keepalived-2.2.7
drwxr-xr-x. 2 root root 4096 Feb 24 20:22 keepalived-2.2.7
drwxr-xr-x. 2 root root 4096 Feb 24 20:22 keepalived-2.2.7
```

## Setup:

server1 - 10.100.1.21

server2 - 10.100.1.22

virtual ip - 10.100.1.20
```
server1# cat /etc/keepalived/keepalived.conf
```
```
vrrp_instance VI_1 {
        state MASTER
        interface eth0
        virtual_router_id 51
        priority 255
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              10.100.1.20/24
        }
}
```
```
server2# cat /etc/keepalived/keepalived.conf
```
```
vrrp_instance VI_1 {

        state BACKUP
        interface eth0
        virtual_router_id 51
        priority 254
        advert_int 1
        authentication {
              auth_type PASS
              auth_pass 12345
        }
        virtual_ipaddress {
              10.100.1.20/24
        }
}
```

If you’re using a host-based firewall, such as firewalld or iptables, then you need to add the necessary rules to permit IP protocol 112 traffic. Otherwise, Keepalived’s advertisement method won’t work. With the above configuration in place, you can start Keepalived on both servers using systemctl start keepalived and observe the IP addresses on each machine.
```
server1# ip -brief address show
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 10.100.1.21/24 10.100.1.20/24 fe80::5054:ff:fe82:d66e/64
```
```
server2# ip -br a
lo UNKNOWN 127.0.0.1/8 ::1/128
eth0 UP 10.100.1.22/24
fe80::5054:ff:fe04:2c5d/64
```