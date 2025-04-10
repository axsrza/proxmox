# 🛡️ Configuração de Firewall - nftables

```bash
sudo apt install nftables -y
sudo systemctl enable nftables
sudo systemctl start nftables
```

### 📜 Regras no `/etc/nftables.conf`

```nftables
table inet filter {
    chain input {
        type filter hook input priority filter; policy drop;

        # Permitir loopback
        iif "lo" accept

        # Permitir conexões estabelecidas
        ct state established,related accept

        # SSH
        tcp dport 22 ct state new accept

        # DNS (UDP e TCP)
        udp dport 53 accept
        tcp dport 53 ct state new accept

        # NGINX no blog (porta 8888)
        tcp dport 8888 ct state new accept

        # Pi-hole Admin e serviços web
        tcp dport 80 ct state new accept
        tcp dport 443 ct state new accept

        # NTP (Network Time Protocol)
        udp dport 123 accept

        # DHCP (caso esteja usando o Pi-hole como DHCP Server)
        udp dport 67 accept

        # Logar e contabilizar o que for bloqueado
        log prefix "[FIREWALL DROP] " flags all counter
    }

    chain forward {
        type filter hook forward priority filter; policy drop;
    }

    chain output {
        type filter hook output priority filter; policy accept;
    }
}
```

### 💾 Salvar regras atuais para o boot:

```bash
sudo nft list ruleset > /etc/nftables.conf
```

