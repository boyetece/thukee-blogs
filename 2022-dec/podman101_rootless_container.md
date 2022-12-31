# Podman 101- rootless container
A container that does not have a root process running in the background which run on privileged  or access. This approach is stem from a security perspective, that it is dangerous for a process to run as a root, and that it might be compromise
during its life cycle. Rootless container allow normal, unprivileged user to run container/s but without the ability to perform tasks that
requires priviledge access.