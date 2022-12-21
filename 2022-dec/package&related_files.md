# Package & Libraries related files
August 09, 2022 by RB

It’s always been a challenge to know which libraries are involved in running a package or executable. Thus, ldd command is used in determining such challenges. This command prints the shared objects (shared libraries) required by each program or shared object specified on the command line.
```
$ which ls

$ ldd /bin/ls
```
```
linux-vdso.so.1 (0x00007ffcc3563000) libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f87e5459000) libcap.so.2 => /lib64/libcap.so.2 (0x00007f87e5254000) libc.so.6 => /lib64/libc.so.6 (0x00007f87e4e92000) libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f87e4c22000) libdl.so.2 => /lib64/libdl.so.2 (0x00007f87e4a1e000) /lib64/ld-linux-x86-64.so.2 (0x00005574bf12e000) libattr.so.1 => /lib64/libattr.so.1 (0x00007f87e4817000) libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f87e45fa000)
```
## Security:

Be aware that in some circumstances (e.g., where the program specifies an ELF interpreter other than `ld-linux.so`), some versions of `ldd` may attempt to obtain the dependency information by attempting to directly execute the program, which may lead to the execution of whatever code is defined in the program's ELF interpreter, and perhaps to execution of the program itself. (In glibc versions before 2.27, the upstream ldd implementation did this for example, although most distributions provided a modified version that did not.)

Thus, you should never employ ldd on an untrusted executable, since this may result in the execution of arbitrary code. A safer alternative when dealing with untrusted executables is:
```
$ objdump -p /path/to/program | grep NEEDED
```

## Information about a package and related files (Ubuntu)

`dpkg-query` actions See `dpkg-query(1)` for more information about the following actions.
```
-l, --list package-name-pattern...

List packages matching given pattern.
-s, --status package-name...

Report status of specified package.
-L, --listfiles package-name...

List files installed to your system from package-name.
-S, --search filename-search-pattern...

Search for a filename from installed packages.
-p, --print-avail package-name...
```
Display details about package-name, as found in
`/var/lib/dpkg/available`. Users of APT-based frontends should use `apt-cache show package-name` instead.

## Information about a package and related files (RedHat)
```
rpm -qf - Uses a filename s its arguments to find the specific RPM packages a file belongs to.

rpm -ql - Uses the RPM database to provide a list of files in th RPM package.

rpm -qi - Uses the RPM database to provide packakge information (equivalent to yum info).

rpm -qd - Uses the RPM database to show all documentation that is available in the package.

rpm -qc - Uses the RPM databse to show all configuration files that are available in the package.

rpm -q --scripts - Uses the RPM database to show scripts that are used in the package. this is particularly useful if combined with the -p option.

rpm -qp <package> - The -p option is used with all the previously listed options listed to query individual RPM packagefiles instead of the RPM package database.
Using this option before installation helps you find out what is actually in the package before it is installed.

rpm -qR - Shows dependencies for a specific package.
```