# Projeto Homelab - Azzor1337x

Transformando um notebook antigo em um homelab funcional, moderno e enxuto 🚀

---

## 🧭 Primeiros passos após instalação

Após finalizar a instalação do Debian 12 minimal, no terminal local logar com o usuário criado, verifique o IP e Interface de Rede:

```bash
ip -c a
```

Usando o IP acima, conecte-se via PuTTY em outro computador na mesma rede, usando o usuário criado no momento da instalação e defina senha root:

```bash
sudo passwd root
```

Logar como root com a senha criada acima:

```bash
su
```

### 1. Instalar Docker Engine e Docker Compose

#### 📌 Fonte: https://docs.docker.com/engine/install/debian/

```bash
# Remover pacotes antigos
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done

# Adicionar a chave GPG oficial do Docker
sudo apt-get update
sudo apt-get install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Adicionar o repositório Docker
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

# Instalar Docker Engine e plugins
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Testar instalação
sudo docker run hello-world
```

---

### 2. Instalar Docker Compose (standalone)

#### 📌 Fonte: https://docs.docker.com/compose/install/standalone/

```bash
# Baixar a versão standalone do Docker Compose
curl -SL https://github.com/docker/compose/releases/download/v2.34.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

# Criar link simbólico
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# Verificar versão instalada
docker compose version
```

---


# 5. Adicionar Pi-hole + Unbound

## Instalar o container Unbound (DNS recursivo)

### Criar a pasta de configuração:
```bash
mkdir -p /homelab/dns/unbound
cd /homelab/dns
```

### Criar o arquivo de configuração `unbound.conf`:
```bash
nano unbound/unbound.conf
```
Conteúdo:
```conf
server:
    verbosity: 1
    interface: 0.0.0.0
    port: 53
    do-ip4: yes
    do-ip6: no
    do-udp: yes
    do-tcp: yes
    hide-identity: yes
    hide-version: yes
    harden-glue: yes
    harden-dnssec-stripped: yes
    use-caps-for-id: yes
    edns-buffer-size: 1232
    prefetch: yes
    num-threads: 1
    msg-cache-slabs: 1
    rrset-cache-slabs: 1
    infra-cache-slabs: 1
    key-cache-slabs: 1
    rrset-cache-size: 100m
    msg-cache-size: 50m
    so-rcvbuf: 1m
    so-sndbuf: 1m
    cache-min-ttl: 3600
    cache-max-ttl: 86400
    val-clean-additional: yes
    val-permissive-mode: no
    val-log-level: 1
    auto-trust-anchor-file: "/opt/unbound/etc/unbound/root.key"
    root-hints: "/opt/unbound/etc/unbound/root.hints"
    qname-minimisation: yes
    minimal-responses: yes
    aggressive-nsec: yes

    access-control: 0.0.0.0/0 allow
    username: ""
```

### Criar o container do Unbound:
```bash
docker run -d --name unbound \
  -v "/homelab/dns/unbound:/opt/unbound/etc/unbound" \
  -p 53:53/udp -p 53:53/tcp \
  --restart unless-stopped \
  mvance/unbound:latest
```

### Testar resolução:
```bash
dig @127.0.0.1 google.com
```

---

## Habilitar validação DNSSEC (recomendado)

### Baixar root hints:
```bash
curl -o root.hints https://www.internic.net/domain/named.root
```

### Copiar para o container:
```bash
docker cp root.hints unbound:/opt/unbound/etc/unbound/root.hints
```

### Gerar arquivo root.key com unbound-anchor:
```bash
docker exec -it unbound sh
unbound-anchor -a "/opt/unbound/etc/unbound/root.key"
exit
```

### Reiniciar o container:
```bash
docker restart unbound
```

### Testar DNSSEC:
- Domínio **válido**:
```bash
dig +dnssec +multi @127.0.0.1 A cloudflare.com
```
- Domínio com DNSSEC **quebrado** (esperado `SERVFAIL`):
```bash
dig +dnssec +multi @127.0.0.1 SOA dnssec-failed.org
```

---

## Próximo passo
Agora que o Unbound está funcionando com DNSSEC, podemos seguir para configurar o Pi-hole usando o Unbound como *upstream DNS*.

