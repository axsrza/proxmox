
```bash

# Limpar regras existentes
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X

# Política padrão: segurança máxima
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Loopback (localhost)
iptables -A INPUT -i lo -j ACCEPT

# Conexões estabelecidas e relacionadas
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# SSH (ajuste o IP permitido se quiser mais segurança)
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
# iptables -A INPUT -p tcp -s 192.168.1.0/24 --dport 22 -j ACCEPT

# Portainer (painel web na porta 9000)
iptables -A INPUT -p tcp --dport 9000 -j ACCEPT

# Blog (NGINX na porta 8888)
iptables -A INPUT -p tcp --dport 8888 -j ACCEPT

# DNS (Pi-hole, público na LAN)
iptables -A INPUT -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT

# Unbound (DNS recursivo local, apenas localhost)
iptables -A INPUT -i lo -p tcp --dport 5335 -j ACCEPT
iptables -A INPUT -i lo -p udp --dport 5335 -j ACCEPT

# Docker: liberar bridge principal (docker0)
iptables -A INPUT -i docker0 -j ACCEPT
iptables -A FORWARD -i docker0 -j ACCEPT

# Ping (ICMP)
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT

# Log de pacotes bloqueados (opcional e limitado)
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables dropped: " --log-level 4

# Salvar regras para persistência
iptables-save > /etc/iptables/rules.v4

# (Opcional) garantir persistência no boot
sudo apt install iptables-persistent -y
sudo netfilter-persistent save

```
