# [DokuWiki](https://www.dokuwiki.org/dokuwiki) 
 Is a simple to use and highly versatile Open Source wiki software that doesn't require a database. It is loved by users for its clean and readable syntax. The ease of maintenance, backup and integration makes it an administrator's favorite. Built in access controls and authentication connectors make DokuWiki especially useful in the enterprise context and the large number of plugins contributed by its vibrant community allow for a broad range of use cases beyond a traditional wiki.


## Update your Ubuntu 22.04 server.
```
~$ sudo apt update
~$ sudo dist-upgrade
```

## Install modules,  apache and php.
~$ sudo apt-get install -y apache2 php php-gd php-xml php-json

## Start and enable apache service.
```
~$ sudo systemctl enable --now apache2
```

## Download DokuWiki files from the official repo and extract the gzip file.
```
~$ cd /var/www
~$ sudo wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz
~$ sudo tar xvf dokuwiki-stable.tgz
~$ sudo mv dokuwiki-*/ dokuwiki
```

## Change ownership and permission for the directory.
```
~$ sudo chown -R www-data:www-data /var/www/dokuwiki
```

## Change document root in Apache to point to /var/www/dokuwiki.
```
~$ sudo vim /etc/apache2/sites-enabled/000-default.conf
```
## Replace it with the following:
```
DocumentRoot /var/www/html
with
DocumentRoot /var/www/dokuwiki
```
## Change allowOverrides setting in apache to use .htaccess files for security.
```
~$ sudo vim /etc/apache2/apache2.conf
```
## For directory `/var/www/` and replace.
```
AllowOverride None
with
AllowOverride All
```

## Restart apache service
```
~$ sudo systemctl restart apache2
```
## Visit http://ip-address/install.php to initially configure your DokuWiki.

## Remove the install.php file after done installing.
```
~$ sudo rm /var/www/dokuwiki/install.php
```