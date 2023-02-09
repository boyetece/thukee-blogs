# HAProxy
## Building reverse proxy with TLS termination between four backend server.

Steps:

1. Perepare the backend servers to accept connection.
1. Prepare a server for haproxy install (Don't use container in this install).
1. Edit the configuration using `mode http` (Layer 7) to better manage the SNI rather than `mode tcp` passthrough (Layer 3) which means using the HAProxy as a dumb reverse proxy and the traffic is evaluated by the backend server itself.
1. Build your DNS from your registrar and point to the correct HAProxy IP address.
1. When using an appliance firewall to do NAT, use port 80 for portforwading.
1. Install LetsEncrypt and create a private and public certificate.
1. Combine the certificate and add them to the HAProxy configuration file.

## Block example for the haproxy.cfg was taken from `Hussein Nasser` git hub profile. 
```
frontend httpsandhttp
   bind *:80
   bind *:443 ssl crt /Users/HusseinNasser/proxy/haproxy.pem alpn h2,http/1.1
  # bind *:443 
   timeout client 60s
   mode http
   acl app1 path_end -i /app1
   acl app2 path_end -i /app2
   http-request deny if { path -i -m beg /admin }  
   use_backend app1Servers if app1
   use_backend app2Servers if app2
   http-response set-header Strict-Transport-Security max-age=16000000;\ includeSubDomains;\ preload;
   default_backend allservers


backend app1Servers
   timeout connect 10s
   timeout server 10s
   balance source
   mode http
   server server2222 127.0.0.1:2222
   server server3333 127.0.0.1:3333
   
backend app2Servers
   timeout connect 10s
   timeout server 300s
   mode http
   server server4444 127.0.0.1:4444
   server server5555 127.0.0.1:5555 
  
   
backend allservers
   timeout connect 10s
   timeout server 100s
   mode http
   server server2222 127.0.0.1:2222
   server server3333 127.0.0.1:3333
   server server4444 127.0.0.1:4444
   server server5555 127.0.0.1:5555

```

## Installing LetsEncrypt and creating a certificate.

```
$ dnf install letsencrypt
```
## Create Certificate:
```
$ certbot certonly --standalone
```
## In This stage you will be promted with the following:
1. email
2. agree to register
3. share your details
4. you domain name
5. It tells that the private and public certificates are generated.

Copy the location of the certificates in a directory of your choosing and combine them using `cat` and `tee` command with pipe between them. Then, copy the location of the new combined certificate file `.pem` to `/etc/haproxy/haproxy.cfg` file (as seen above example). The `alpn h2,http/1.1` beside it means Application Layer Protocol Notification that supports H2 and HTTP 1.1.

