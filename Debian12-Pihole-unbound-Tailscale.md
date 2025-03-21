## Denifir Senha Root

```bash

sudo passwd root

```

## Logar como Root

```bash

su

```

## Acessar Raiz

```bash

cd /

```

## Atualizar Tudo

```bash

apt update && apt upgrade

```

## Baixar curl btop

```bash

apt install curl btop

```

## Configurar Ip Fixo

```bash

nano /etc/network/interfaces

```

## Colar abaixo # The primary network interface

```bash

allow-hotplug enx00e04c680094
iface enx00e04c680094 inet static
    address 192.168.1.99
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 192.168.1.1

```

## Atualizar Time Zone

```bash

timedatectl set-timezone America/Sao_Paulo

```

## Reiniciar

```bash

systemctl reboot

```

## Logar como Root

```bash

su

```

## Acessar Raiz

```bash

cd /

```

## Instalar Pi-hole

```bash

curl -sSL https://install.pi-hole.net | bash

```

## Definir Senha Pi-hole

```bash

pihole setpassword

```

## Instalar unbound

```bash

apt install unbound

```

## Colar no Terminal

```bash

wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints

```

## Config unbound

```bash

nano /etc/unbound/unbound.conf.d/pi-hole.conf

```

## Config unbound

```bash

server:
    # If no logfile is specified, syslog is used
    # logfile: "/var/log/unbound/unbound.log"
    verbosity: 0

    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes

    # May be set to no if you don't have IPv6 connectivity
    do-ip6: yes

    # You want to leave this to no unless you have *native* IPv6. With 6to4 and
    # Terredo tunnels your web browser should favor IPv4 for the same reasons
    prefer-ip6: no

    # Use this only when you downloaded the list of primary root servers!
    # If you use the default dns-root-data package, unbound will find it automatically
    #root-hints: "/var/lib/unbound/root.hints"

    # Trust glue only if it is within the server's authority
    harden-glue: yes

    # Require DNSSEC data for trust-anchored zones, if such data is absent, the zone becomes BOGUS
    harden-dnssec-stripped: yes

    # Don't use Capitalization randomization as it known to cause DNSSEC issues sometimes
    # see https://discourse.pi-hole.net/t/unbound-stubby-or-dnscrypt-proxy/9378 for further details
    use-caps-for-id: no

    # Reduce EDNS reassembly buffer size.
    # IP fragmentation is unreliable on the Internet today, and can cause
    # transmission failures when large DNS messages are sent via UDP. Even
    # when fragmentation does work, it may not be secure; it is theoretically
    # possible to spoof parts of a fragmented DNS message, without easy
    # detection at the receiving end. Recently, there was an excellent study
    # >>> Defragmenting DNS - Determining the optimal maximum UDP response size for DNS <<<
    # by Axel Koolhaas, and Tjeerd Slokker (https://indico.dns-oarc.net/event/36/contributions/776/)
    # in collaboration with NLnet Labs explored DNS using real world data from the
    # the RIPE Atlas probes and the researchers suggested different values for
    # IPv4 and IPv6 and in different scenarios. They advise that servers should
    # be configured to limit DNS messages sent over UDP to a size that will not
    # trigger fragmentation on typical network links. DNS servers can switch
    # from UDP to TCP when a DNS response is too big to fit in this limited
    # buffer size. This value has also been suggested in DNS Flag Day 2020.
    edns-buffer-size: 1232

    # Perform prefetching of close to expired message cache entries
    # This only applies to domains that have been frequently queried
    prefetch: yes

    # One thread should be sufficient, can be increased on beefy machines. In reality for most users running on small networks or on a single machine, it should be unnecessary to seek performance enhancement by increasing num-threads above 1.
    num-threads: 1

    # Ensure kernel buffer is large enough to not lose messages in traffic spikes
    so-rcvbuf: 1m

    # Ensure privacy of local IP ranges
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10

```

## Teste unbound

```bash

sudo service unbound restart
dig pi-hole.net @127.0.0.1 -p 5335

```

## Configurar Ip Fixo + Local DNS

```bash

nano /etc/network/interfaces

```

## Colar abaixo # The primary network interface

```bash

allow-hotplug enx00e04c680094
iface enx00e04c680094 inet static
    address 192.168.1.99
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 127.0.0.1

```

## Configurar Local DNS

```bash

nano /etc/resolv.conf

```

## Config Local DNS

```bash

nameserver 127.0.0.1

```

## Reiniciar

```bash

systemctl reboot

```

## Logar como Root

```bash

su

```

## Acessar Raiz

```bash

cd /

```

## Instalar Tailscale

```bash

curl -fsSL https://tailscale.com/install.sh | sh

```

## Forward ipv4 e ipv6

```bash

echo 'net.ipv4.ip_forward = 1' | tee -a /etc/sysctl.d/99-tailscale.conf

echo 'net.ipv6.conf.all.forwarding = 1' | tee -a /etc/sysctl.d/99-tailscale.conf

sysctl -p /etc/sysctl.d/99-tailscale.conf

```

## Executar Tailscale

```bash

tailscale up --accept-dns=false --advertise-routes=192.168.1.0/24 --advertise-exit-node

```
