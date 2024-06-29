## Instalação adguard

```bash
sudo mkdir -p /adguard/work
sudo mkdir -p /adguard/conf
cd /adguard

sudo docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=enp5s0 adguard

sudo docker run --name adguardhome \
    --restart unless-stopped \
    --network adguard \
    --ip 192.168.1.101 \
    -v /adguard/work:/opt/adguardhome/work \
    -v /adguard/conf:/opt/adguardhome/conf \
    -p 53:53/tcp -p 53:53/udp \
    -p 67:67/udp -p 68:68/udp \
    -p 80:80/tcp -p 443:443/tcp -p 443:443/udp -p 3000:3000/tcp \
    -p 853:853/tcp \
    -p 784:784/udp -p 853:853/udp -p 8853:8853/udp \
    -p 5443:5443/tcp -p 5443:5443/udp \
    -d adguard/adguardhome

```
## docker com porta diferente

```bash
sudo mkdir -p /adguard/work
sudo mkdir -p /adguard/conf
cd /adguard

sudo docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=enp5s0 adguard

sudo docker run --name adguardhome \
    --restart unless-stopped \
    --network adguard \
    --ip 192.168.1.112 \
    -v /adguard/work:/opt/adguardhome/work \
    -v /adguard/conf:/opt/adguardhome/conf \
    -p 54:53/tcp -p 54:53/udp \
    -p 67:67/udp -p 68:68/udp \
    -p 8080:80/tcp -p 443:443/tcp -p 443:443/udp -p 3000:3000/tcp \
    -p 853:853/tcp \
    -p 784:784/udp -p 853:853/udp -p 8853:8853/udp \
    -p 5443:5443/tcp -p 5443:5443/udp \
    -d adguard/adguardhome

```
