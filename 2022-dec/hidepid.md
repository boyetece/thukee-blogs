# How to hide PID listings from non-root users in Linux
August 07, 2022 by RB

To prevent average users from viewing your Linux system's processes with the hidepid command.

In general, what runs on your server should be considered private information that is shared only on a need-to-know basis. If there's no reason for a user to have insight into what services are running on a server, then a user probably shouldn't have permission to view process ID (PID) listings.

## Finding a service
It's common, by default, for a regular Linux account (emad in this example) to be able to view a PID listing using ps, pgrep, pidof, and so on:
```
$ sudo su – emad
$ ps -ef | wc -l
229
```
## A user usually can see all processes. It's a lot of output, but if a user is searching for something specific, such as database system processes such as PostgreSQL (a popular open source database), it's pretty easy to find:
```
$ ps -ef | grep postgres
postgres  1143 [...] /usr/pgsql-12/bin/postmaster -D /var/lib/pgsql/12/data/
postgres  1151 [...] postgres: logger
postgres  1153 [...] postgres: checkpointer
postgres  1154 [...] postgres: background writer
postgres  1155 [...] postgres: walwriter
postgres  1156 [...] postgres: autovacuum launcher
postgres  1157 [...] postgres: stats collector
postgres  1158 [...] postgres: logical replication launcher
```
Not everyone needs to see what processes are running, so I use hidepid.
Use hidepid to hide processes

## To prevent a user from seeing all the processes running on a system, mount the /proc file system using the hidepid=2 option:
```
$ sudo mount -o remount,rw,nosuid,nodev,noexec,relatime,hidepid=2 /proc
```

## The hidepid parameter value accepts three values:

* 0: This is the default. Every user can read all world-readable files stored in a process directory.
* 1: Root process directories remain listed in /proc but are not accessible to users. Users can access only their own process directories. This protects sensitive files like cmdline, sched, or status from access by non-root users. This setting does not affect the actual file permissions. 
* 2: Process files are invisible to non-root users. The existence of a process can be learned by other means, but its effective user ID (UID) and group ID (GID) are hidden.
```
$ ps -ef | wc -l
63
$ ps -ef | grep postgres
emad 7091  7067  0 14:02 pts/0  00:00:00 grep --color=auto postgres
```
The directories representing PIDs are removed from `/proc`. The user emad can no longer view PostgreSQL database system process ID's.

## Need to know

Instead of changing mount options as your system runs, add the hidepid option to `/etc/fstab`.

Securing your operating system is a continuous challenge, and with servers containing highly confidential data, it's important to think about what you want people to be able to stumble across on your system. Use hidepid to remove processes from casual inspection.