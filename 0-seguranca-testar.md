
```bash

# Limpar regras existentes
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X

# Política padrão: bloquear tudo
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Permitir conexões locais (loopback)
iptables -A INPUT -i lo -j ACCEPT

# Permitir conexões já estabelecidas e relacionadas
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Permitir SSH (caso use)
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Permitir acesso HTTP do blog (porta 8888)
iptables -A INPUT -p tcp --dport 8888 -j ACCEPT

# Permitir acesso ao Portainer (porta 9000)
iptables -A INPUT -p tcp --dport 9000 -j ACCEPT

# Permitir DNS local (Pi-hole)
iptables -A INPUT -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -p udp --dport 53 -j ACCEPT

# Permitir acesso ao Unbound (DNS recursivo na 127.0.0.1:5335, só local)
iptables -A INPUT -i lo -p tcp --dport 5335 -j ACCEPT
iptables -A INPUT -i lo -p udp --dport 5335 -j ACCEPT

# Permitir tráfego Docker bridge (se necessário)
iptables -A INPUT -i br-124625da573a -j ACCEPT
iptables -A INPUT -i br-971eb000a6b2 -j ACCEPT
iptables -A FORWARD -i br-124625da573a -j ACCEPT
iptables -A FORWARD -i br-971eb000a6b2 -j ACCEPT

# Ping (opcional)
iptables -A INPUT -p icmp --icmp-type echo-request -j ACCEPT

# Log (opcional)
# iptables -A INPUT -j LOG --log-prefix "iptables input drop: "

# Salvar regras
iptables-save > /etc/iptables/rules.v4
```
