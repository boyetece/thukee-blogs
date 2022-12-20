# RHCSA v8 Practice Exam:

## Important Instructions:
```
hostname: servera.lab.example.com (172.25.250.10)
hostname: serverb.lab.example.com (172.25.250.11)
```
    1. You will be given two VM’s
    2. There will be a total of 20 questions
    3. On one server a system root password is already set (no need for a reset)
    4. You need to reset the second VM using the password ‘thanos’, note that both should have the same password.
    5. On one VM, network configuration is required, the other vm is already set.
    6. One VM should be configured for NTP service, the other is already set.
    7. A remote repository should be configure for both VM’s.
    8. LDAP client is already configured for both VM, you only need to configure automounting for LDAP user’s home directory on one VM.
    9. Firewall and Selinux are all pre-enabled.

## For servera.lab.example.com (172.25.250.10)

### Q1. Configure network and set the static hostname:
	IP ADDRESS  = 172.25.250.10
	NETMASK     = 255.255.255.0
	GATEWAY     = 172.25.250.254
	DNS         = 172.25.250.254
	Domain Name = lab.example.com

### Q2.	Configure YUM repository with given link:
	base_url= http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
	AppStream_url= http://content.example.com/rhel8.0/x86_64/dvd/BaseOS/AppStream

### Q3.	Debug Selinux:
>A web server running on a non standard port 82 is having a issue serving content. Debug a fix the issue. The web server on your system can serve all existing html files from var/www/html (Note: Do not make any changes to these files.) The web service should automatically start on boot time.
	
### Q4.	Create a user accounts with supplementary group.

    • Create the group and named `sysadms`
    • Create users as `natasha` and `harry`, and have them their supplementary group as ‘sysadms’.
    • Create a user named ‘sarah’, and should have a non-interactive shell and should not be a member of ‘sysadms’.
    • password for all users should be ‘thanos’.

### Q5.	Configure a cron job that runs every 1 minute and executes:
	
	It should display “EX200 in progress” using user ‘natasha’.

### Q6.	Create a collaborative Directory.

    • Create a directory ‘/home/manager’ with the following characteristics.
    • Group ownership of ‘home/manager’ should be ‘sysadms’ group.
    • The directory should have full permission for all members of the ‘sysadms group’, but not to other users except ‘root’.
    • Files created in the future under ‘/home/manager’ should get the same group ownership.


### Q7.	Configure NTP service.

    • Synchronize time of your system with the server classroom.example.com.

### Q8.	Configure AutoFS

    • All ldapuserX home directory is exported via NFS, which is available on classroom.example.com (172.25.254.254) and the NFS-export directory is /home/guests for ldapuserX).
    • ldapuserX’s home directory is classroom.example.com:/home/guests/ldapuserX, where X is your station number.
    • ldapuserX’s home directory should be automounted in autofs service.
    • Home directories must be writable by their users.
    • While you are able to log is as any of the user ldapuser1 through ldapuser20, the only home directory that is accessible from your system is ldapuserX ( your station number).

### Q9.	Access Control List (ACL).

    • Copy the file /etc/fstab to /var/tmp and configure the ‘ACL’ as mentioned in the following:
    • The file /var/tmp/fstab should be owned by the ‘root’.
    • The file /var/tmp/fstab should belong to the group ‘root’.
    • The file /var/tmp/fstab should not be executable by any one.
    • User ‘sarah’ should be able to read and write to the file.
    • User ‘harry’ can neither read nor write to the file.
    • Other users (future and current) should be able to read /var/tmp/fstab.

### Q10.	Create user : 
> `bob` with `uid of 2112` and set the password `thanos`

### Q11.	Locate all files owned by user :
>  `harry` and copy it under `/root/harry-files`

### Q12.	Find a string :
> `ich` from the `/usr/share/dict/words` and put it into `/root/lines` file.

### Q13.	Create an archive :
>  `/root/backup.tar.bz2` on `/usr/local` directory and compress it with bzip2.


## For serverb.lab.example.com (172.25.250.11)

	Note: In this server, 3 disk will be given.

    1. /dev/vda	:	for root filesystem (don’t do anything under this disk).
    2. /dev/vdb	:	use this for swap and LVM partitions.
    3. /dev/vdc	:	will be used for Statis Storage.	

### Q14.	Reset root password using :
> `thanos`

### Q15.	Configure yum repository :
	
	base_url= http://content.example.com/rhel8.0/x86_64/dvd/BaseOS
	AppStream_url= http://content.example.com/rhel8.0/x86_64/dvd/BaseOS/AppStream

### Q16.	Resize a logical Volume :

	Resize the logical volume ‘mylv’ so that after reboot, the size should be in between 200MB to 300MB.

### Q17.	Add a swap partition : 
`of 512MB and mount it permanently.`

### Q18.	Create a logical Volume and mount it permanently :

    • Create the logical volume with the name ‘wshare’ by using 50PE’s from the volume group ‘wgroup’.
    • Consider each PE size of volume group as ‘8MB’.

### Q19A. Create a new STRATIS volume according to the following requirements:

    • Use the unpartitioned disk.
    • The Volume is named ‘stratisfs’ which belongs to ‘stratispool’.
    • The Volume must be mounted permanently under ‘/stratisvolume’.
    • Place a copy of the file in ‘http://content.example.com/file.txt’ under ‘/stratisvolume’.
    • Take a snapshot of the stratisfs named stratissnap.

### Q19B. Create a new VDO partition using the following requirements:

    • use the unpartitioned disk
    • vdo name is ‘vdo1’ and the logical size should be 50GB.
    • Mount it on ‘/vdomount’ permanently with file system of xfs. 

### Q20. Configure System Tuning:

    • Choose the recommended ‘tuned’ profile for your system and set it as the default.

