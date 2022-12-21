 STRATIS Storage:

Stratis is a volume management XFS filesystem to easily configure pools and filesystems with enhanced storage functionality that works within the existing Linux block device such as LVM but not on partitions.

Features are:

1. Thin Provisioning
1. Snapshots
1. Cache-Tier
1. Programatic API
1. Monitoring and Repair

 To achieve this, Stratis prioritizes a straightforward command-line experience, a rich API, and a fully automated approach to storage management. It builds upon elements of the existing storage stack as much as possible. Stratis has also incorporate additional technologies in the such as vdo (RedHat 9.0+). Stratis can configure an encrypted or unencrypted pool of storage with one or more file systems quickly and without prior knowledge of the many storage layers and commands.

# Commands to remember:

1. Create a stratis pool.
1. Create a filesystem on that pool.
1. Create a directory to mount the create stratis filesystem.
1. Query the block device.
1. Query the stratis pool.

## Installing stratis storage package:
```
stratisstorageserver ~$ sudo dnf install stratisd stratis-cli -y
```

## Enable and Restart Stratis Stratis Service:
```
stratisstorageserver ~$ sudo systemctl enable --now stratisd
```

## Confirm available block device/s for stratis storage to use:
```
stratisstorageserver ~$ sudo lsblk
```

# Procedures:
## Note: Block device must be at least 1GB and paritions are not supported.

## Create a stratis pool:
```
~$ stratis pool create stratispool /dev/sdb
```
## Create stratis filesystem on the pool:
```
~$ stratis fs create stratispool statisfs
```
## Check the filesystem created.
```
~$ stratis fs list
```
## Create a directory for the filesystem:
```
~$ mkdir /database
```
## Mount the the filesystem to that directory:
```
~$ mount /dev/stratis/stratispool/stratisfs /database
```
or mount it in the `fstab` file to be permanent.

## Check the pool usage:
```
~$ stratis pool list
~$ stratis blockdev list stratispool
```
# To Expand and rename existing filesystem and pool:
## Expand existing pool, rename a filesystem or destroy filesystem and pool:
```
~$ stratis pool add-data stratispool /dev/sdc
```
## Rename existing pool:
```
~$ stratis pool rename stratispool storagepool
```
## Rename existing filesystem:
```
~$ stratis fs rename storagepool stratisfs storagefs
```
## Destroy filesystem and pool:
```
~$ sudo umount ~/database

~$ stratis fs destroy storagepool storagefs

~$ stratis pool destroy storagepool
```