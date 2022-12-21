
# NGINX with PHP module

August 13, 2022 by RB

The PHP Fast Process Manager (php-fpm) is a FastCGI handler for PHP scripts and applications. It’s commonly paired with web servers to serve applications that require a PHP framework, such as web forums or login gateways, while the web server returns HTML, JavaScript, and other non-PHP content.

Assuming that you have already installed an nginx package and using the default `www-data` user as the default user for nginx. We can check by executing this command:
```
$ ps -aux | grep nginx
root      3448  0.0  0.0  32500  3516 ?        Ss   18:21   0:00 nginx: master process /        usr/sbin/nginx -c /etc/nginx/nginx.conf
www-data     3603  0.0  0.0  32912  2560 ?        S    18:24   0:00 nginx: worker process
www-data     3604  0.0  0.0  32912  3212 ?        S    18:24   0:00 nginx: worker process
```
Then, we can now proceed in installing the php-fpm module to support php content.

## Installing php-fpm: 
```
$ sudo apt install php-fpm
```
## To check for version:
```
$ sudo php -v
```
## Checking for Status:
```
$ sudo systemctl status php8.1-fpm.servce
```
## These are the configuration files location:
```
$ find / \( -iname "php.ini" -o -name "www.conf" \)

/etc/php/8.1/fpm/php.ini
/etc/php/8.1/fpm/pool.d/www.conf
/etc/php/8.1/cli/php.ini
```

In some documentation/How-to-Guides, we need to change the listen.owner and listen.group as well as owner and group in the www.conf file. But since we use the www-data as a default user we don't need to change these directives.

## Security Consideration:

It’s important to limit what NGINX passes to PHP-FPM so malicious scripts can’t be injected into return streams to the server. Instead, the request is stopped, possibly then resulting in a 404.

In the `/etc/php/8.1/fpm/pool.d/www.conf` replace the following directive from:

`cgi.fix_pathinfo=1` to `cgi.fix_pathinfo=0`

## We can now restart the module:
```
$ systemctl restart php8.1-fpm.service
```

## Common nginx location configuration block when serving both .html and .php files

File: `/etc/nginx/conf.d/web.conf`
```
server {
    listen         80 default_server;
    listen         [::]:80 default_server;
    server_name    example.com www.example.com;
    root           /var/www/example.com;
    index          index.php index.html;

  location ~* \.php$ {
    fastcgi_pass unix:/run/php/php8.1-fpm.sock;
    include         fastcgi_params;
    fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
  }
}
```

This is just a bare minimum to get PHP-FPM working and you will want to configure it further for your specific needs. Some further points about the configuration above:

The location `~* \.php$` means that NGINX will apply this configuration to all `.php` files (file names are not case sensitive) in your site’s root directory, including any subdirectories containing PHP files.

The `*` in the `~* \.php$` location directive indicates that PHP file names are not case sensitive. This can be removed if you prefer to enforce the letter case.

The `fastcgi_pass` location must match the `listen = value` in `/etc/php/7.0/fpm/pool.d/www.conf`. It is preferable for performance reasons for PHP-FPM to listen on a UNIX socket instead of a TCP address. Only change this if you require PHP-FPM to use network connections.

Using `$document_root` in the SCRIPT_FILENAME parameter instead of an absolute path is preferred by NGINX’s documentation.

## Restarting nginx:
```
$ nginx -s reload
```
## Notes to remember:

All directories files on your `/var/www/html` must have this permission:

Directories should have `755` permissions.

Files should have `644` permissions.
