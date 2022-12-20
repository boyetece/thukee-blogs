#SELINUX Concepts:

Discretionary Access Control:

- Ownership and Permissions (Standard, Special and ACL)

Mandatory Access Control:

- SElinux

There are default Policies already installed for the following:
1. Users
2. Files
3. Directories
4. Memories
5. Sockets
6. Ports
etc...

Type of Policy:
1. Targeted - this is the default policy applied.
2. MLS - multi-level/muliti-category security - only see it in mlitary or complex security requirements.

Two important concepts of selinux:
1. Labeling - Files, Process, ports, etc..,are all labeled with an selinux context.
    - For files and directories, these labels are stores as extended attributes on the filesystem using -Z option on the commands.
    - For processes and ports, etc..,the kernel manages these labels.
    - Format is as follows:
                            user:role:type:level(optional)
Note: We will focus on the type, as USER,ROLE and LEVEL are used in more advanced implementation.

2. Enforcement Type


How to check on what policy the system is set on (ENFORCING, PERMISSIVE OR DISABLED) in three ways:
server~$ cat /etc/selinux/config
OR
server~$ sestatus
OR
server~$ getenforce





