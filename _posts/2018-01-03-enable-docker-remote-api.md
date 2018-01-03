This article describes how to configure a docker to be able to connect to it over tcp.

## Enable the remote API on a new socket

Create a file called `/etc/systemd/system/docker-tcp.socket` to make Docker available on a TCP socket on port 2375.

```
[Unit]
Description=Docker Socket for the API

[Socket]
ListenStream=2375
BindIPv6Only=both
Service=docker.service

[Install]
WantedBy=sockets.target
```

Then enable this new socket:
```bash
systemctl enable docker-tcp.socket
systemctl stop docker
systemctl start docker-tcp.socket
systemctl start docker
```

Test that it's working:
```bash
docker -H tcp://127.0.0.1:2375 ps
```

## Connect from another machine

On another machine use DOCKER_HOST env variable (`docker-host` is an address of a docker machine):
```bash
DOCKER_HOST=tcp://docker-host:2375 docker ps
```

From https://coreos.com/os/docs/latest/customizing-docker.html
