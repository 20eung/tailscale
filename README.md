# tailscale
```
$ cat docker-compose.yml
version: "2.4"
services:
  tailscale:
      privileged: true
      hostname: tailscale                                    # This will become the tailscale device name
      network_mode: "host"
      container_name: tailscale
      image: tailscale/tailscale:latest
      volumes:
          - "/opt/appdata/tailscale/var_lib:/var/lib"        # State data will be stored in this directory
          - "/dev/net/tun:/dev/net/tun"                      # Required for tailscale to work
      cap_add:                                               # Required for tailscale to work
        - net_admin
        - sys_module
      command: tailscaled
      restart: unless-stopped
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
