## Aumentar o LOCAL storage do Proxmox e remover LOCAL-LVM (terminal)

```bash
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root
```

## Remover mensagem de SUBSCRIPTION do Proxmox (terminal)

```bash
sed -Ezi.bak "s/(Ext.Msg.show\(\{\s+title: gettext\('No valid sub)/void\(\{ \/\/\1/g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy.service
```

## Atualizar tudo (terminal)

```bash
sudo apt update && sudo apt upgrade && sudo apt dist-upgrade
```

## Ativar Windows / Office (powershell)

```bash
irm https://massgrave.dev/get | iex
```

## Scrcpy Wifi (powershell)

```bash
.\adb tcpip 5555
.\adb connect 192.168.1.1:5555
.\scrcpy -s 192.168.1.1:5555
```

## alterar

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/debian.sh)"
sudo apt update
sudo passwd root
sudo apt install net-tools
ifconfig
sudo nano /etc/ssh/sshd_config
PermitRootLogin yes
sudo service ssh restart
```
