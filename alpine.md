
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
