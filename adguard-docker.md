## Instalação adguard

```bash
cd /
cd
mkdir -p /root/adguard/work
mkdir -p /root/adguard/conf
cd /root/adguard

docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=enp5s0 mynet

docker run --name adguardhome \
    --restart unless-stopped \
    --network mynet \
    --ip 192.168.1.112 \
    -v $(pwd)/work:/opt/adguardhome/work \
    -v $(pwd)/conf:/opt/adguardhome/conf \
    -p 8080:80/tcp -p 443:443/tcp -p 3001:3000/tcp \
    -d adguard/adguardhome

```