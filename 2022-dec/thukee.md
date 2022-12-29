# Documentation for Thukee Blog Site and it's requirements:
## Background:
```
                                                        AlmaLinux
                                     |---------------------------------------------|
                                     |                                             | Firewall
[clients] -----> [CloudFlare] -----> |[haproxy] ------> [thukee website container] | Blocking:
     {tcp,tls,https}           tcp   |   |        http    Port:8000                | 8000,8001
                                     |   |                                         | 
                                     |   |------------> [kavita reader container]  |
                                     |            http    Port: 8001               |
                                     |---------------------------------------------| 
```



## Deploying HAProxy for reverse proxy to nginx-thukee and kavita Reader.
We have use AlmaLinux as our docker Host because Linode does not have a RHEL VPS image.
Using HAProxy `v2.4.17` was used to focus on singular service that is for reverse proxy.
1. Configuration File: `/etc/haproxy/haproxy.cfg`
```
global
    ##maxconn 50000 - max connection from running out of memory.
    ##log stdout local0 - enable log to systemd/rsyslog.
    ##stats socket :9000 mode 650 level admin - enable runtime API, health checks, change server weights and other dynamic changes. 
    
    maxconn 50000
    log stdout local0
    stats socket :9000 mode 650 level admin

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
    use_backend thukee if { req.hdr(host) -i thukee.com }
    use_backend kavita if { req.hdr(host) -i kavita.thukee.com }

backend thukee
    server thukeesite 127.0.0.1:8000

backend kavita
    server kavitareader 127.0.0.1:8001
    
```

`Note: Be sure to add `<span style="color:blue">thukee.dev1</span>`  and  `<span style="color:blue">kavita-dev1</span>` as new entry on your internal dns server if your going to use this in your internal network.`

Start and enable the haproxy service
```
~$ systemct enable --now haproxy.service
```
Enable `http` service in the firewall.
```
~$ firewall-cmd --permanent --add-service=http
~$ firewall-cmd --list-all
~$ firewall-cmd --reload
```
Test it through curl or browser should return :
```
503 Service Unavailable
No server is available to handle this request.
```
This is because no active webserver running yet.

`Note: It is possible that if selinux is on enforcing mode. it will preventing `<span style="color:blue">/usr/sbin/haproxy</span>`  from name_connect access on the tcp_socket port  `<span style="color:blue">8000</span>` and other higher port number. Thus the best way is to enable the seboolean for haproxy. You can start with the logs to verify it.`

```
~$ journalctl -f
```
When it is verified that it is causing it we can execute this:
`as suggested in the journalctl logs`
```
~$ setsebool -P haproxy_connect_any 1
```


# Deploying NGINX with Docker

## Managing Content and Configuration Files
Maintain the Content and Configuration on the Docker Host. When the container is created we can tell Docker to mount a local directory on the Docker host to a directory in the container. The NGINX image uses the default NGINX configuration, which uses `/usr/share/nginx/html` as the container’s root directory and puts configuration files in `/etc/nginx`. For a Docker host with content in the local directory `/var/www` and configuration files in `/var/nginx/conf`.

## docker run for nginx
`Note: Be sure to fix the SElinux file context in your host  `<span style="color:blue">var/www</span>`  and  `<span style="color:blue">var/nginx/conf</span>`  directories before creating the nginx container. Not doing so would result a  `<span style="color:blue">403 Forbiden http error</span>` . Or we can automatically fix this with   `<span style="color:blue">:Z</span>` at the end of each defined volumes.`

```
podman run --name thukee -p 8000:80 \
    -v /container/nginx/www:/usr/share/nginx/html:Z \
    -v /container/nginx/conf:/etc/nginx/conf:Z \
    --restart unless-stopped \
    -d nginx:stable
```
 Any change made to the files in the local directories /var/www and /var/nginx/conf on the Docker host are reflected in the directories /usr/share/nginx/html and /etc/nginx in the container. The readonly option means these directories can be changed only on the Docker host, not from within the container.

[NGINX Blog](https://www.nginx.com/blog/deploying-nginx-nginx-plus-docker/?fbclid=IwAR3HxLLX5Vm2D1mB9NIoAP-Pwovj25FZoAn8DQRJGoonKnjVasYTfPOuV6k)

[NGINX Documentation](https://www.haproxy.com/documentation/hapee/latest/configuration/config-sections/backend/)


# Deploying Kavita Reader Server

## podman run for kavita
```
podman run --name kavita -p 8001:5000 \
    -v /container/kavita/manga/ebooks:/manga:Z \
    -v /container/kavita/data/config:/kavita/config:Z \
    --restart unless-stopped \
    -d kizaing/kavita:latest
```
Alternatively, you can use docker compose. But since we're using AlmaLinux which is a RHEL derivative. We will stick to `podman run`.

## docker-compose for kavita
```
version: '3.9'
services:
    kavita:
        image: kizaing/kavita:latest
        volumes:
            - ./manga:/manga:Z
            - ./data:/kavita/config:Z
        environment:
            - TZ=America/Winnipeg
        ports:
            - "8001:5000"
        restart: unless-stopped
```