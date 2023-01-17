# Searching for Shared Object Library (.so)

**Shared Object libraries** - are libraries needed for a binary to function as intended. It is a must to know this seemingly trevial wierd extension. But very powerful implemetation of Linux Kernel and it's development. Thus, how do we find libraries connected to a particular binary? It is important to know that there is one binary that can do this, but unknowingly tuck inside a very powerful ***`glibc`***.  binary tool. That is ***`ldd`*** ,  it prints the shared objects (shared libraries) required by each program or shared object specified on the command line. 

## First, we can inspect a binary and it's following files association.

```
$ sudo rpm -q --list nfs-utils
OR
$ sudo rpm -ql nfs-utils
OR
$ sudo dnf repoquery -l glibc-common
```
We see that the output shows all the associated files and directories. But not the libraries itself. This is where  ***`ldd`***  is needed. But we must install the ***`glibc-common`***  if not installed yet.

Here, we can verify that ***`ldd`*** binary is included in the ***`glibc-common`*** package.
```
$ sudo rpm -q --provides glibc-common
AND
$ sudo rpm -q --list glibc-common
/usr/bin/catchsegv
/usr/bin/gencat
/usr/bin/getconf
/usr/bin/getent
/usr/bin/iconv
/usr/bin/ld.so
/usr/bin/ldd
/usr/bin/locale
/usr/bin/localedef
/usr/bin/pldd
/usr/bin/sotruss
/usr/bin/sprof
/usr/bin/tzselect
/usr/bin/zdump
```
Let's try the ***`locate`*** binary to check the libraries it uses.
```
$ sudo ldd /usr/bin/locate
        linux-vdso.so.1 (0x00007ffd26b8d000)
	    libc.so.6 => /lib64/libc.so.6 (0x00007fd25c3fc000)
	    /lib64/ld-linux-x86-64.so.2 (0x00007fd25c616000)
```
`However, there a security implications in running `***ldd***`  command on untrusted binary. Please check the  `***man page***`  for more detailed explanation on this.`

There's also a great explanation about shared libraries here:

[***`TheUrbanPenguin`***](https://www.youtube.com/watch?v=CqUuNCZMGJU)