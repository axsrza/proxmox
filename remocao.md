## Remoção e Desativação de Serviços no Linux

Este guia detalha a função de cada serviço ou pacote desabilitado/remoção no sistema operacional Debian/Ubuntu para uso em homelabs otimizados e minimalistas.

---

### 1. Desabilitar Serviços via `systemctl`

```bash
sudo systemctl disable \
  bluetooth.service \
  networking.service \
  anacron.service \
```

**Funções detalhadas:**
- **bluetooth.service**: Gerencia conexões Bluetooth. Desnecessário em servidores sem interface sem fio.
- **networking.service**: Sistema tradicional de gerenciamento de redes (usado com ifupdown). Substituído por `systemd-networkd` ou `netplan`.
- **anacron.service**: Executa tarefas perdidas do `cron` se o sistema estiver desligado. Desnecessário em servidores que ficam ligados.

---

### 2. Remoção de Pacotes com `apt`

#### 2.1 Comando:
```bash
sudo apt remove --purge anacron bluetooth ifupdown -y
```

- **anacron**: Ver item anterior.
- **bluetooth**: Remove suporte completo a dispositivos Bluetooth.
- **ifupdown**: Ferramenta antiga para gerenciamento de interfaces de rede. Substituído pelo `systemd-networkd`.

#### 2.2 Comando:
```bash
sudo apt purge \
  avahi-autoipd discover discover-data \
  inetutils-telnet emacsen-common \
  iamerican ibritish ienglish-common ispell dictionaries-common \
  debian-faq doc-debian installation-report iw -y
```

**Função de cada pacote:**
- **avahi-autoipd**: Atribui IP automático (zeroconf). Pode interferir com IPs estáticos.
- **discover / discover-data**: Detecta hardware automaticamente no boot. Redundante para servidores fixos.
- **inetutils-telnet**: Cliente telnet, protocolo obsoleto e inseguro.
- **emacsen-common**: Dependência de instalação de editores Emacs. Supérfluo.
- **iamerican / ibritish / ienglish-common / ispell / dictionaries-common**: Dicionários e corretores ortográficos.
- **debian-faq / doc-debian**: Documentação oficial, removida para economizar espaço.
- **installation-report**: Ferramentas de diagnóstico do instalador Debian.
- **iw**: Ferramentas de configuração de dispositivos Wi-Fi, desnecessárias sem hardware compatível.

#### 2.3 Comando:
```bash
sudo apt autoremove --purge -y
```

Remove dependências não utilizadas deixadas por outros pacotes.

---

### 3. Limpeza de Serviços Systemd

#### 3.1 Reset de falhas pendentes:
```bash
sudo systemctl reset-failed
```

Evita que falhas passadas interfiram nos logs ou status atual.

#### 3.2 Remoção de arquivos systemd supérfluos:
```bash
sudo find /etc/systemd/system /lib/systemd/system \
  -name '*auditd*' -o -name '*connman*' -o -name '*console-screen*' \
  -o -name '*display-manager*' -o -name '*firewalld*' \
  -o -name '*NetworkManager*' -o -name '*plymouth*' \
  -o -name '*syslog*' -o -name '*hwdb*' -o -name '*oomd*' \
  -o -name '*update-done*' -o -name '*vconsole*' -delete
```

**Funções comuns dos arquivos removidos:**
- **auditd**: Demonstração de auditoria do sistema.
- **connman**: Gerenciador de conexão (Wi-Fi/Ethernet).
- **console-screen / display-manager / plymouth**: Interfaces gráficas e bootsplash.
- **firewalld / NetworkManager**: Sistemas mais pesados para rede e firewall, trocados por ferramentas mais leves.
- **syslog**: Logs do sistema, substituídos pelo `journald`.
- **hwdb / oomd / update-done / vconsole**: Configurações de hardware, memória, teclado e status do update.

---

### 4. Mascaramento de serviços

```bash
sudo systemctl disable \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service

sudo systemctl mask \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service

sudo systemctl mask e2scrub_all.service e2scrub_reap.service
```

**Explicação:**
- **wpa_supplicant**: Responsável por conexões Wi-Fi. Mascarado para impedir sua ativação.
- **e2scrub**: Verificação de integridade de sistemas de arquivos ext4. Pode ser desnecessário se já houver backups e manutenção preventiva.

---

### 5. Recarregar `systemd` e limpar logs

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo journalctl --rotate
sudo journalctl --vacuum-time=1s
```

**Função:**
- **daemon-reexec / reload**: Atualiza o `systemd` após modificações.
- **journalctl rotate/vacuum**: Força a rotação e limpeza de logs antigos.

---

### 6. Reiniciar o sistema

```bash
sudo reboot
```

Aplica as mudanças feitas no sistema de forma limpa e segura.
