# Podman 101 - Container

Container is launched with root user priviledges. This gives full access to perform administrative functions including the ability to map network ports that are lower than 1024.
With this, also comes with potential danger that containers are compromised due to a vulnerability of misconfiguration.


# PRIVILEDGE Container:
## Install container-tools
```
user@thukee ~$ sudo dnf search container-tools
```
## Check the registries included in the registriees.conf under the user's home directory
```
user@thukee ~$ cat ~/.config/containers/registries.conf
```
## List of podman sub commands:

`images, inspect, login/logout, pull, rmi, search`

## List of container management:
`attch, exec, generate, info, inspect, ps, rm, run, stop/start/restart`

## skopeo
This tool is used to interact for the image file, either local or remote
images and registries. mostly you will only be using the `inspect` subcommand.
```
user@thukee ~$ skopeo inspect {image name}
```
# Container Management:
## Pull an image from remote registry
```
[user@thukee ~]$ podman search alma9-ubi
.
.
.
[user@thukee ~]$ podman pull docker://quay.io/alma9-ubi
.
.
.
[user@thukee ~]$ podman images
```

## Execute a command inside the container
```
[user@thukee ~]$ podman exec alma9-ubi cat /etc/os_release
```
## Attach to a running container:
```
[user@thukee ~]$ podman attach alma9-ubi
[root@5g678uh9345 /]#
```
## Deleting an existing container:
```
[user@thukee ~]$ podman rm alma9-ubi
.
.
.
[user@thukee ~]$ ps -a
```
## Running a container with persistent storage:
options:

`-v`  is for volume that is defined from host to container directories.

`-p`  is for port number

`-Z`  is for SELinux container context

`-d`  is for detach mode


`note: be sure to create `[/container/nginx/www](#)`  and  `[/container/nginx/conf](#)`  directories in the host before executing the command below.
`
```
[user@thukee ~]$ podman run --name thukee -p 8000:80 -v /container/nginx/www:/usr/share/nginx/html:Z -v /container/nginx/conf:/etc/nginx/conf:Z --restart unless-stopped -d nginx:stable 
```
## Running other environment variables:
options:

`-it`  is for interactive terminal
`e`   is for environment variables

```
[user@thukee ~]$ podman run -it -e HISTFILE -e SECRET="password" --name mysql mysql:stable
```