# Deploying HAProxy for reverse proxy to nginx-thukee and kavita Reader.
We have use AlmaLinux as our docker Host because Linode does not have a RHEL VPS image.
Using HAProxy was use to focus on singular service that is to reverse proxy.
1. Configuration File: `/etc/haproxy/haproxy.cfg`
```
global
    maxconn 50000 ##max connection from running out of memory.
    log stdout local0 ##enable log to systemd/rsyslog.
    stats socket :9000 maode 650 level admin ##enable runtime API, health checks, change server weights and other dynamic changes. 

defaults
    ## mode http - defines the mode if it's layer 4 (`tcp` proxy) or layer 7 (`http` proxy).
    ##log global - instruct haproxy to use log directive define from the global section above.
    ##option httplog - tells haproxy log verbose messages for http traffic.
    ##option forwardfor - records client source ip address and places it in `xforwardedfor` header.

    mode http
    log global
    option httplog
    option forwardfor
    timeout connect 10s
    timeout client 10s
    timeout server 10s

frontend www
    bind :80
    use_backend thukee if {hdr(host) -i thukee.com}
    use_backend kavita if {hdr(host) -i kavita.thukee.com}

backend thukee
    server thukeesite 127.0.0.1:8000

backend kavita
    server kavitareader 127.0.0.1:8001
    
```

# Deploying NGINX with Docker

## Managing Content and Configuration Files
Maintain the Content and Configuration on the Docker Host. When the container is created we can tell Docker to mount a local directory on the Docker host to a directory in the container. The NGINX image uses the default NGINX configuration, which uses `/usr/share/nginx/html` as the container’s root directory and puts configuration files in `/etc/nginx`. For a Docker host with content in the local directory `/var/www` and configuration files in `/var/nginx/conf`.

## docker run for nginx
Note: Be sure to fix the SElinux file context in your host `/var/www` and `/var/nginx/conf` directories before creating the nginx container. Not doing so would result a `403 Forbiden` http error.
```
podman run --name thukee -p 8000:80 \
    -v /var/www:/usr/share/nginx/html \
    -v /var/nginx/conf:/etc/nginx/conf \
    --restart unless-stopped \
    -d nginx:stable
```
 Any change made to the files in the local directories /var/www and /var/nginx/conf on the Docker host are reflected in the directories /usr/share/nginx/html and /etc/nginx in the container. The readonly option means these directories can be changed only on the Docker host, not from within the container.

[NGINX Blog](https://www.nginx.com/blog/deploying-nginx-nginx-plus-docker/?fbclid=IwAR3HxLLX5Vm2D1mB9NIoAP-Pwovj25FZoAn8DQRJGoonKnjVasYTfPOuV6k)


# Deploying Kavita Reader Server

## docker run for kavita
```
docker run --name kavita -p 8001:5000 \
    -v /your/manga/directory:/manga \
    -v /kavita/data/directory:/kavita/config \
    --restart unless-stopped \
    -d kizaing/kavita:latest
```


## docker-compose for kavita
```
version: '3.9'
services:
    kavita:
        image: kizaing/kavita:latest
        volumes:
            - ./manga:/manga
            - ./data:/kavita/config
        environment:
            - TZ=America/Winnipeg
        ports:
            - "8001:5000"
        restart: unless-stopped
```