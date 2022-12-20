# NFS and AutoFS for Direct Map share:

Is a distributed file system protocol originally developed by Sun Microsystems (Sun) in 1984, allowing a user on a client computer to access files over a computer network much like local storage is accessed. NFS, like many other protocols, builds on the Open Network Computing Remote Procedure Call (ONC RPC) system. NFS is an open IETF standard defined in a Request for Comments (RFC). `NFS4 (RFC 3010, December 2000; revised in RFC 3530, April 2003 and again in RFC 7530, March 2015)`, allowing anyone to implement the protocol.

Install NFS package:
```
nfserver ~$ sudo dnf install nfs-utils -y
```
Create a network share:
```
nfserver ~$ sudo mkdir /share
nfserver ~$ sudoo chmod 777 /share
```

Add nfs service to the firewall daemon:
```
nfserver ~$ sudo firewall-cmd --add-service nfs --permanent
nfserver ~$ sudo firewall-cmd --reload
```

Enable and start nfs service:
```
nfserver ~$ sudo systemctl enable --now nfs-server
nfserver ~$ sudo systemctl status nfs-server
```

Create an nfs exports config file add the following:
```
nfserver ~$ vi /etc/exports
/share  nfsclient(rw)
```

Reload the nfs service exports file:
```
nfserver ~$ sudo exportfs -av
```

## AutoFS or AutoMount is a client-side service, which is employed to mount and unmount an NFS share on-demand as compared with manually mounting and unmounting NFS share.

## Direct Map - Creating an AutoFs or Automount:
Direct Map is used to mount shares automatically, always visible to users, local and mounted shares can coexist under one parent directory.


Install autofs package:
```
nfs-client ~$ sudo install autofs -y
```

Create a moount point:
```
nfs-client ~$ sudo mkdir /autoNFS
```

Edit the `/etc/auto.master` file and add the following directive:
```
nfs-client ~$ sudo vi /etc/auto.master
/~  /etc/auto.master.d/auto.NFS
```

Create the `auto.NFS` file and add the follwing directive:
```
nfs-client ~$ sudo vi /etc/auto.master.d/auto.NFS
/autoNFS    nfserver:/share
```

Start and enable the AutoFS service:
```
nfs-client ~$ sudo systemctl enable --now autofs
```

Run the `ls` command and check the mount using the `mount` command:
```
nfs-client ~$ ls /autoNFS
nfs-client ~$ mount | grep autoNFS
```



# NFS and AutoFS for In-Direct Map share:
## AutoMounting User Home Directories:

### Note: 
    1. Be sure to install nfs-utils before installing autofs.
    2. users name with it's corresponding `uid` and `gid` must be the same on both nfs server and nfs client computer.

## NFS service side:
Install NFS package:
```
nfserver ~$ sudo dnf install nfs-utils -y
```

Create a user called `user30` with `UUID 3000` and assign a password:
```
nfserver ~$ sudo useradd -u 3000 user30
nfserver ~$ sudo echo 12345678 | sudo passwd --stdin user30
```

Add nfs service to the firewall daemon:
```
nfserver ~$ sudo firewall-cmd --add-service nfs --permanent
nfserver ~$ sudo firewall-cmd --reload
```

Enable and start nfs service:
```
nfserver ~$ sudo systemctl enable --now nfs-server
nfserver ~$ sudo systemctl status nfs-server
```

Edit the `exports` file and add the directive:
```
nfserver ~$ sudo vi /etc/exports
/home   nfs-client(rw)
```

Export the share in the `/etc/exports` file:
```
nfserver ~$ sudo exportfs -avr
```

## NFS Client side:
Install AutoFs package.
```
nfs-client ~$ sudo dnf install autofs -y
```

Create a user called `user30` with `UUID 3000` and assign a password.
```
nfs-client ~$ sudo useradd -u 3000 user30
nfs-client ~$ sudo echo 12345678 | sudo passwd --stdin user30
```

Create an umbella mount point to automount the user's home directory:
```
nfs-client ~$ sudo mkdir /nfshome
```

Edit the `/etc/auto.master` file and add the `/nfshome` directory.
```
nfs-client ~$ sudo vi /etc/auto.master
/nfshome    /etc/auto.master/auto.home
```

Create the `/etc/auto.master.d/auto.home` file and add the directive.
```
nfs-client ~$ sudo vi /etc/auto.master.d/auto.home
*   -rw     nfserver:/home/&
```