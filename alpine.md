
# Internet + DNS

```
nano /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
    address 192.168.1.99
    netmask 255.255.255.0
    gateway 192.168.1.1
```

```
nano /etc/resolv.conf
```

```
nameserver 192.168.1.1
```

```
ifdown eth0 && ifup eth0
```

```
ip a show eth0
```

```
ping -c 3 1.1.1.1
```

```
ping -c 3 google.com
```

# Firewall

```
apk add iptables
```

# Docker

```
nano /etc/apk/repositories
```

```
#/media/sdb1/apks
http://dl-cdn.alpinelinux.org/alpine/v3.21/main
http://dl-cdn.alpinelinux.org/alpine/v3.21/community
```

```
apk update
apk add docker
```
