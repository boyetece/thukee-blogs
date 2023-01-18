# BCC-Tools
 This is a dynamic kernel tracing tools, called bcc-tools, this tool is for checking system performance - `extended Berkeley Packet Filter (eBPF)` used by system administrators.  It allows dynamic kernel tracing without requiring kernel modules (like systemtap) or rebooting of the kernel. It accomplishes this while maintaining minimal overhead for each trace point, making these tools an ideal way to check the metrics in the kernel that are running in production.
 
## This is the following process when started by root on the command line:

* The program is compiled into eBPF bytecode.

* Loaded into the kernel.

* Run through a technology called the eBPF verifier to ensure that the 
    program  will not harm the running kernel.

* Upon passing the verifier, it begins execution. If it does not pass        the verifier, the code is unloaded and does not execute.

## Installing bcc-tools:
```
root@server~$ dnf install bcc-tools 
```
`Note: All of these tools live in `/usr/share/bcc/tools `.`

## Category of tools under bcc-tools:

* The Snoops
* Latency Detectors
* Slower
* Top Up with bcc-tools
* Java/Perl/Python/Ruby

## You can execute the binaries under the `/usr/share/bcc/tools` directories.
```
root@server~$ cd /usr/share/bcc/tools/
root@server~$ ls
```
## There are 120 list of tools available:

| Tools: |  |  |  |  |  |  |  |
---| ---| ---| ---|---| ---| ---| ---|
| argdist | bitesize | cpudist | deadlock.c | ext4slower | funcslower | javastat | mdflush |
| nfsslower | perlcalls | profile | rubycalls | runqslower | stackcount | tclobjnew | tcpretrans |
| threadsnoop | wakeuptime | bashreadline | bpflist | cpuunclaimed | dirtop | filelife | gethostlatency | 
| javathreads | memleak | nodegc | perlflow | pythoncalls  | rubyflow | shmsnoop | statsnoop | 
| tclstat | tcprtt | tplist | xfsdist | bindsnoop | cachestat | dbslower | doc | 
| fileslower | hardirqs | killsnoop | mountsnoop | nodestat   | perlstat | pythonflow | rubygc | 
| slabratetop | swapin | tcpaccept | tcpstates | trace | xfsslower | biolatency | cachetop | 
| dbstat | drsnoop | filetop | javacalls | klockstat | mysqld_qslower | offcputime | phpcalls | 
| pythongc | rubyobjnew | sofdsnoop | syncsnoop | tcpconnect | tcpsubnet | ttysnoop | biolatpcts | 
| capable | dcsnoop | execsnoop | funccount | javaflow       | kvmexit | netqtop | offwaketime | 
| phpflow | pythonstat  | rubystat | softirqs | syscount   | tcpconnlat | tcpsynbl | vfscount | 
| biosnoop | cobjnew | dcstat | exitsnoop | funcinterval | javagc | lib | netqtop.c | 
| oomkill | phpstat | readahead | runqlat | solisten | tclcalls  | tcpdrop | tcptop | 
| vfsstat | biotop | compactsnoop | deadlock | ext4dist | funclatency  | javaobjnew | llcstat | 
| nfsdist | opensnoop | pidpersec | reset-trace | runqlen | sslsniff  | tclflow | tcplife | 
| tcptracer | virtiostat 
|

## To determine each description, use the `man` page.
```
root@server~$ cd /usr/share/bcc/tools/
root@server~$ man argdist
```
[BCC-Tools Reference](https://blogs.oracle.com/linux/post/block-layer-observability-with-bcc-tools)