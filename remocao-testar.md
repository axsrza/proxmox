### 1. Desabilitar Serviços via `systemctl`

```bash
sudo systemctl disable \
  bluetooth.service \
  networking.service \
```

**Funções detalhadas:**
- **bluetooth.service**: Gerencia conexões Bluetooth. Desnecessário em servidores sem interface sem fio.
- **networking.service**: Sistema tradicional de gerenciamento de redes (usado com ifupdown). Substituído por `systemd-networkd` ou `netplan`.

---

### 2. Remoção de Pacotes com `apt`

#### 2.1 Comando:
```bash
sudo apt remove --purge bluetooth ifupdown wpasupplicant -y
```

- **wpasupplicant**: Utilizado para redes Wi-Fi. Inútil em servidores cabeados.
- **bluetooth**: Remove suporte completo a dispositivos Bluetooth.
- **ifupdown**: Ferramenta antiga para gerenciamento de interfaces de rede. Substituído pelo `systemd-networkd`.

---

### 3. Limpeza de Serviços Systemd

#### 3.1 Reset de falhas pendentes:
```bash
sudo systemctl reset-failed
```

- **firewalld / NetworkManager**: Sistemas mais pesados para rede e firewall, trocados por ferramentas mais leves.

---

### 4. Mascaramento de serviços

```bash
sudo systemctl disable \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service && \
sudo systemctl mask \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service

```

**Explicação:**
- **wpa_supplicant**: Responsável por conexões Wi-Fi. Mascarado para impedir sua ativação.

---

### 5. Recarregar `systemd` e limpar logs

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo journalctl --rotate
sudo journalctl --vacuum-time=1d
```

---

### 6. Reiniciar o sistema

```bash
sudo reboot
```

```bash
sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean
```
