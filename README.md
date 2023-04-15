# tailscale
 

> $ cat docker-compose.yml

```
version: "2.4"
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: tailscale
    restart: unless-stopped
    privileged: true
#   network_mode: "host"
    environment:
      - TZ=Asia/Seoul
    volumes:
      - /dev/net/tun:/dev/net/tun
      - /opt/appdata/tailscale/var_lib:/var/lib
      - /etc/timezone:/etc/timezone
      - /var/run/dbus/system_bus_socket:/var/run/dbus/system_bus_socket
    cap_add:
      - net_admin
      - sys_module
    command: tailscaled
```

## 1. tailscale docker-compose up

```
$ docker-compose up -d
```

## 2. enable ip forwarding

```
$ echo 'net.ipv4.ip_forward = 1' | sudo tee -a /etc/sysctl.conf
$ echo 'net.ipv6.conf.all.forwarding = 1' | sudo tee -a /etc/sysctl.conf
$ sudo sysctl -p /etc/sysctl.conf
```

## 3. login use authkey

tailscale admin page: https://login.tailscale.com/admin

![Auth key](./img/authkeys.PNG "Authkeys")

Settings > Personal Settings > Keys > Auth keys > Generate auth key... > copy

```
$ docker exec tailscale tailscale up --authkey=tskey-kFYpHy2CNTRL~~~~~~~
```

## 4. enable exit node

network_mode = host 로 설정하면 exit node 가 정상적으로 동작하지 않는다.

```
$ docker exec tailscale tailscale up --advertise-exit-node --accept-routes
```

tailscale admin page : https://login.tailscale.com/admin

![Exit node](./img/exitnode.PNG "Exit node")

... click > Edit route settings... > Exit node > enable : Use as exit node

## 5. connect docker container

```
docker exec -it tailscale /bin/sh
```
