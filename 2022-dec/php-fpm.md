# php-fpm with Apache on RHEL 9.1
Reason behind this documentation is that `fpm-php` is used instead of `mod_php` because, starting from release `5.3.3 in early 2010` , PHP has merged the php-fpm fastCGI process manager into its codebase. This means that secure, fast, and dependable PHP code can run now using only the stock Apache HTTPD and PHP.net releases.

## PHP FastCGI Process Manager (FPM):
* Search for available version of php-fpm on your repo:
```
root@server~$ dnf repoquery -l php-fpm
```
* If your satisfied with the version, you can now proceed in installing it.

`Note: This goes the same on how to search for httpd (apache2 for debian) version. But if your want the latest version, you can pull it on the official php website`
```
root@server~$ dnf install php-fpm
```
* These are the configuration paths for the are RHEL derivatives:
```
    /etc/httpd/ -> We'll be working with this file.
    /etc/php.ini
    /etc/php-fpm.conf
    /etc/php-fpm.d/www.conf -> And this!
```
* We'll start with the `www.conf` file and We modify it, by commenting the socket and add the port instead.
```
root@server~$ vi /etc/php-fpm.d/www.conf
```
* commenting the socket and add the port.
```
;listen = /run/php-fpm/www.sock
listen=127.0.0.1:9000
```
* Now, we go to the `httpd conf` file and add new directives:
```
root@server~$ vi /etc/httpd/conf.d/php.conf
```
* Add this at the bottom.
```
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so
Listen 8080
<VirtualHost *:8080>
  ServerAdmin webmaster@localhost
  ServerName yourservername
  DocumentRoot /website/php-website/
  ErrorLog logs/web-error_log
  CustomLog logs/web-access_log common
  LogLevel debug 
  AddType application/x-httpd-php .php
  <Directory /website/php-website>
     Options FollowSymlinks ExecCGI Includes
     DirectoryIndex index.php
     AllowOverride All
     Require all granted
  </Directory>
<LocationMatch "^/(.*\.php(/.*)?)$">
  ProxyPass  fcgi://127.0.0.1:9000/website/php-website/$1
</LocationMatch>
</VirtualHost>
```
`Note: In this setup we are using the httpd as our proxy at port 8080 pointing to the loopback ip address with port 9000`

* Now we can create the the `DocumentRoot` as defined in our directives above.
```
root@server~$ mkdir -p /website/php-website/ | sudo touch /website/php-website/index.php
 ```
 * We can add this inside the `index.php`, to verify that the server API has indeed change to  `FPM/FastCGI`.
 ```
<?php phpinfo() ?>
 ```
 * Since we're working on a RHEL derivative platform, we need to enable `SELinux file context` . But before that, We will check if the boolean for the httpd is set.
```
root@server~$  getsebool -a | grep httpd_can_network_connect
```
If the output is `off`, it means it's disabled. Then we will enable it and add the `content type`  as well and also the `restore context`,  the `restore context`  will  make file changes in the future in this directory maintain the define `SELinux file context` .
```
root@server~$ setsebool -P httpd_can_network_connect 1

root@server~$ semanage fcontext -a -t httpd_sys_content_t '/website(/.*)?'

root@server~$ restorecon -vvRF /website
```
* Now we can start the `httpd` and `php-fpm` service.
```
root@server~$ start php-fpm
root@server~$ start httpd
```
* We can test the it now in your browser.
```
http://ServerNameOrIP:8080/index.php
```

Addition Troubleshooting:

1. If you want to check the fcontext:
```
root@server~$ semanage fcontext -l | grep httpd_sys_content_t
```
1. If you want to check the SELinux logs, if you suspect there's an issues relating to permissions:
```
root@server~$ journactl -f /var/log/audit/audit.log
```