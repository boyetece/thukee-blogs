# Podman 101- rootless container
A container that does not have a root access privilege running in the background. This approach stem from a security perspective, that it is dangerous for a process to run as a root, and that it might be compromise
during its life cycle. Rootless container allow normal, unprivileged user to run container/s but without the ability to perform tasks that
requires priviledge access.

In this series, we will focus on persitent storage with its corresponding SELinux context and on how it affects the deployment of container/s. Steps will be provided to allow this technologies to work as intented.

## Procedures:

1. Create a directory for the persistent storage in the user1 home subdirectory.
```
user1@server1 ~$ mkdir ~/container/nginx
```
2. Set the permission of the directory to allow `others` to write to this directory.
```
user1@server1 ~$ chmod o+w ~/container/nginx
```
3. Set the ownership of the directory to `user1`.
```
user1@server1 ~$ chown user1:user1 ~/container/nginx
```
4. Run the conainer with `-v` option for persistent volume and add the `Z` option for the SELinux context to work with the persistent volume.
```
user1@server1 ~$ podman run --name nginx -p 8000:80 \
    -v /container/nginx/www:/usr/share/nginx/html:Z \
    -v /container/nginx/conf:/etc/nginx/conf:Z \
    --restart unless-stopped \
    -d nginx:stable
```
5. Checking the logs within podman.
```
user1@server1 ~$ podman logs nginx
```
If there are some problem/s, such as permissions issues if any.

6. Assuming permissions issues were logged. You can check the audit.log of the host if SELinux is affecting the container permissions.
```
user1@server1 ~$ sudo grep AVC /var/log/audit/audit.log
```
We should see SELinux permission logs related to the container and a SELinux solution will be presented on how to deal with it.

7. enable the firewall to open port 8000.
Note: If you dont have a standalone intance of reverse-proxy then port 8000 must be opened rathar than `http service`.
```
user1@server1 ~$ sudo firewall-cmd --add-port=8000 --permanent
user1@server1 ~$ sudo firewall-cmd --reload
```