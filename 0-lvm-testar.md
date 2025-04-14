# Homelab com Debian 12 Minimal + LVM + Snapshots + Docker

## Instalação do Debian com LVM e Snapshots

Durante a instalação do Debian 12 Minimal:

1. Escolha **Modo Expert Install** (se disponível).
2. Configure o teclado e idioma normalmente.
3. Configure rede com IP dinâmico por enquanto.
4. No particionamento, escolha **Manual**.
5. Apague todas as partições do SSD de 120GB.
6. Crie uma nova tabela de partições (GPT).

### Crie as partições:

**1. /boot** (fora do LVM):
- Tipo: `ext4`
- Tamanho: `512MB`
- Ponto de montagem: `/boot`

**2. LVM (restante do disco):**
- Usar como: volume físico LVM

### Configure o LVM

1. Crie um grupo de volumes (VG), exemplo: `vg0`.
2. Dentro do VG `vg0`, crie os volumes lógicos (LV):

```
LV root   -> 20GB   -> /     (ext4)
LV home   -> 50GB   -> /home (ext4)
LV var    -> 10GB   -> /var  (ext4)
LV swap   -> 4GB    -> swap  (swap area)
```

**Importante:** Deixe 30–35GB **livres no VG** para snapshots.

3. Finalize o particionamento e continue a instalação.

---

## Primeiro Boot

```bash
ip -c a
```

```bash
sudo passwd root
su
```

```bash
sudo timedatectl set-timezone America/Sao_Paulo
```

```bash
sudo rm /etc/udev/rules.d/10-network.rules
sudo nano /etc/udev/rules.d/10-network.rules
```

```bash
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="00:e0:4c:68:00:94", NAME="eth0"
```

```bash
sudo rm /etc/systemd/network/10-wired.network
sudo nano /etc/systemd/network/10-wired.network
```

```bash
[Match]
Name=eth0

[Network]
Address=192.168.1.99/24
Gateway=192.168.1.1
DNS=192.168.1.1
#DNS=127.0.0.1
```

```bash
sudo rm /etc/resolv.conf
sudo nano /etc/resolv.conf
```

```bash
nameserver 192.168.1.1
#nameserver 127.0.0.1
```

```bash
sudo rm /etc/network/interfaces
sudo systemctl enable --now systemd-networkd
sudo systemctl restart systemd-networkd
sudo systemctl disable --now networking
sudo reboot
```

---

## Boot com IP Fixo

```bash
sudo apt install iptables btop -y
```

### Instalar Docker

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian   $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
sudo curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo ln -sf /usr/local/bin/docker-compose /usr/bin/docker-compose
sudo docker compose version
```

### Instalar Unbound (DNS Recursivo)

```bash
sudo apt install unbound -y
sudo wget https://www.internic.net/domain/named.root -qO- | sudo tee /var/lib/unbound/root.hints
sudo nano /etc/unbound/unbound.conf.d/pi-hole.conf
```

```bash
# Conteúdo do arquivo
server:
    verbosity: 0
    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes
    do-ip6: yes
    prefer-ip6: no
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: no
    edns-buffer-size: 1232
    prefetch: yes
    num-threads: 1
    so-rcvbuf: 1m
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10
    private-address: 192.0.2.0/24
    private-address: 198.51.100.0/24
    private-address: 203.0.113.0/24
    private-address: 255.255.255.255/32
    private-address: 2001:db8::/32
```

```bash
sudo service unbound restart
dig pi-hole.net @127.0.0.1 -p 5335
```

### Pi-hole via Docker

```bash
sudo mkdir -p ~/homelab/pihole/
sudo nano ~/homelab/pihole/docker-compose.yml
```

```bash
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    network_mode: host
    environment:
      TZ: 'America/Sao_Paulo'
      WEBPASSWORD: ''
    volumes:
      - './etc-pihole:/etc/pihole'
      - './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```

```bash
cd ~/homelab/pihole
sudo docker-compose up -d
docker exec -it pihole pihole setpassword
```

### Alterar DNS do host para Unbound local

```bash
sudo nano /etc/systemd/network/10-wired.network
```

```bash
#DNS=127.0.0.1
```

```bash
sudo nano /etc/resolv.conf
```

```bash
nameserver 127.0.0.1
```

```bash
sudo reboot
```

---

## Instalar Portainer

```bash
sudo mkdir -p ~/homelab/portainer
sudo nano ~/homelab/portainer/docker-compose.yml
```

```bash
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: unless-stopped
    ports:
      - "9000:9000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data

volumes:
  portainer_data:
```

```bash
cd ~/homelab/portainer
sudo docker-compose up -d
```

---

## Comandos de Teste

```bash
ip -c a
hostname -I
docker ps -a
docker images
docker network ls
docker volume ls
docker compose ls
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

---

## Dica: Criando Snapshot Manual do root

```bash
lvcreate -L1G -s -n snap_root /dev/vg0/root
```

Para restaurar:

```bash
umount /
lvconvert --merge /dev/vg0/snap_root
reboot
```

Ou para remover:

```bash
lvremove /dev/vg0/snap_root
```

