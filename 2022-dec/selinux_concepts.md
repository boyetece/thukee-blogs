# SELINUX:

Is a labeling system. Every process has a label. Every file, directory, system object has a label. Policy rules control access between labeled processes and labeled objects.

There are default Policies already installed for the following:

* Users
* Files
* Directories
* Memories
* Sockets
* Ports etc...

## Type of Policy:

1. Targeted - this is the default policy applied.
1. MLS - multi-level/muliti-category security - only see it in mlitary or complex security requirements.

## Two important concepts of selinux:

Labeling - Files, Process, ports, etc..,are all labeled with an selinux context.For files and directories, these labels are stores as extended attributes on the filesystem using -Z option on the commands. For processes and ports, etc..,the kernel manages these labels.
Format is as follows:

`user:role:type:level(optional)`

    Note: 
    We will focus on the type, as user,role and level are used in more advanced implementation.

## Enforcement
This is the enforcement that the labeling is pointing. It defines what kind of enforcement the the labeling context has access to a process running with the label `httpd_t` can have read access to a file labeled `httpd_config_t` type.

Common Commands:
| Command |	Option | Option | Description|
--- | --- | ---| ---|
|restorecon| 	|	|	This is use when you copy a file to the directory that has a predefined selinux context, so that the new file will have the same selinux context on the directory.|
|chcon| | |			This is use when you want to define an selinux context of a file within the new directory created.|
|semanage port| | | 			This is use when you want to add a new port other that that defined in the default selinux policy.|
|semanage fcontext| | | 			This is use when you want to define the same selinux context on files created within the new directory.|
|sestatus| 	0 or 1 |	|	display selinux status ENFORCING, PERMISSIVE OR DISABLED|
|setsebool |	|	|	sets selinux policy, that are defaultly defined 0 or 1|
|getsebool| 	-a |	|	displays selinux policy, that are defaultly defined|
|getenforce 	|0 or 1 |	|	display selinux status ENFORCING, PERMISSIVE OR DISABLED|
|setenforce |	0 or 1 	|	|sets selinux status ENFORCING, PERMISSIVE OR DISABLED
|

## How to check on what policy the system is set on (ENFORCING, PERMISSIVE OR DISABLED) in three ways:

```
server~$ cat /etc/selinux/config
```
OR
```
server~$ sestatus
```
OR
```
server~$ getenforce
```
## How do we deals with labels:

```
server~$ ls -lZ
server~$ id -Z
server~$ ps -Z
server~$ netstat -Z
server~$ cp -Z
server~$ mkdir -Z
server~$ chcon [change context]
server~$ restorecon [restore context]
```
```
Note:
Context are set when files are created,based on their paren directory's context (with few exceptions).
RPM's can set contexts as part of the installation.
Login process sets the default context (unconfined in the targeted policy).
```

## semanage command can be used to manage/label SELinux settings for the following:
```
login
user
port
interface
module
node
file context
boolean
permissive state
dontaudit
```

## SELinux Errors:
```
* It could mean that the labels are wrong
* It could mean that the policy needed to be tweak on booleans, policy modules.
* There could be a bug in the policy.
* Or worst youcould be under attack.
```

## For Booleans: 

Booleans are just off/on settings for SELinux policy on the system. So how do we list SELinux default Policies defined on an active linux system?
```
server~$ getsebool -a
```
```
Note: 
There are a hundreds selinux default policies defined on an active linux system. Piping a grep command will help narrow a particular search.
```

## Special Note: 

Setup a development environment in developing a selinux policy module. And the tools would greatly help diagnose and fix SELinux issues. Modules that would be needed are:
```
server~$ dnf install setroubleshoot setroubleshoot-server
```
and restart the auditd service.

## Examples 1:

A developer luke wants to have his own web page in the /home/luke/public on the webserver.

- Admin enabled the `UserDir` in the `/etc/httpd/conf.d/userdir.conf`
- change to permission o+x to /home/luke directory.
- Admin restart the web server
- But  did not parse the index.php of luke.

What needs to be done here? If we try checking the following logs for the httpd:

- /var/log/httpd/access.log
- /var/log/https/error_log

you will notice that it doesn't help much except there's a permission error. Now if we use the journal control log server~$ journalctl -b -0. We see the exact error on this issue in a bright red letters and more troubleshooting suggestions too.(This only comes out when you install setroubleshoot and setroubleshoot-server). The first option tofix it would be:
```
server~$ setsebool -P httpd_enabled_homedirs=1
```
This policy is already predefined but not set and all we have to do is enable it.


## Examples 2:

A new sysadmin just inherited a new linux system, how can he know what the previous admin on what boolean have been set manually? He can check it inside the:

```
server~$ cat /etc/selinux/targeted/modules/active/booleans.local
```
```
Note: 
Don't edit this directory because when setsebool -P command was used it will just be regenerated after. Editing it won't do anything.
```

## Examples 3:

A new Linux system has been installed with unmodified SELinux environment. A developer has a /home/mark/ directory that he wanted to serve on the webserver.

- Admin move the index.html file to the /var/www/html/
- Change to permission o+x to /home/luke directory.
- Admin restart the web server
- But is did not parse the index.php of mark.

The First issue here is the mv command for move, this will retain the ownership of the file. Thus creating permission issue when moved to /var/www/html/ directory. But even if permission is change accordingly this does not fix the issue as SELinux must be set properly to be able to parse the php content of mark. Again checking the log files:

```
server~$ journalctl -b -0
```

As we check it the context now is different from the previous example. It's because the move command retained the context of the user even though we change the ownership of the file, it didn't change the context which is supposed to be httpd_sys_content_t instead we get user_home_t context. As usual the log will give troubleshooting suggestions. Butwe can so this in multiple ways.

```
server~$ chcon -u system_u -r object_r -t httpd_system_content_t /var/www/html/index.html

server~$ chcon -t httpd_sys_content_t /var/www/html/index.html

server~$ chcon --reference /var/www/html/ /var/www/html/index.html

server~$ restorecon -vR /var/www/html/
```
```
Note: 
restorecon relies on information from the /etc/selinux/targeted/context/files/file_contexts to determine what a file or directory's default context should be. And there 4000 entries in this file. Don't modify.
```

## Examples 3:

Putting webserver files on non-standard /data/public directoryand permissions are set correctly. Then admin define the virtual web site in the httpd.conf and restart the httpd service. But then the /data/public/index.html isn't parse. Again checking the log files:

```
server~$ journalctl -b -0
```

But this time the options for appropriate context are too many to choose. But since the admin know he's' working on a webserver using `httpd_sys_content_t` would be appropriate.

```
server~$ semanage fcontext -a -t httpd_sys_content_t "/data(/.*)?" 
server~$ restorecon -vR /data
```
Checking for all existing directories and files for SElinux context
```
server~$ semanage fcontext -l
```
OR
```
server~$ semanage fcontext -a -e /var/www/html /data 
server~$ restorecon -vR /data
```

## Example 4:

Adding non-standard network port 82 to SELinux context database for httpd service.

```
server~$ semanage port -l | grep ^httpd_port

server~$ semanage port -a -t httpd_port_t -p tcp 82
```

To delete a network port 82 from the SELinux context database.

```
server~$ semanage port -d -p tcp 82
```

## Policy Module:

When everything isn't working for a new installed software on Linux system. We can create a policy module for this. We can always check the `journalctl -b -0` to check the errors and we'll start from there.

If you have confirmed that the SELinux is the cause of the new software not working as it should be. Set the SELinux to 'permissive mode' and run software as it should be.

Then execute the `journalctl -b -0` again. From here, it will tell you how to fix the issue by giving a suggestions. Then `enable` it back and run again the software with errors on it.

```
server~$ setenforce 1
```
## Enabling SELinux

To enable SELinux, edit /etc/selinux/config and set SELINUX=permissive
Create a file 
```
server~$ touch / .autorelabel
```
Reboot the machine


Reference: 

[Redhat Summit](https://www.youtube.com/watch?v=_WOKRaM-HI4&t=614s) 

[SELinux Project](http://selinuxproject.org/page/User_Resources?fbclid=IwAR3TLQXDDPdki2yIopE3_-gme6J4qlrHSanoiVeU1HAZNJwf__g-QQmGJmU)