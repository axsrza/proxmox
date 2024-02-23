## Aumentar o LOCAL storage do Proxmox e remover LOCAL-LVM

```bash
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root
```

## Remover mensagem de SUBSCRIPTION do Proxmox

```bash
sed -Ezi.bak “s/(Ext.Msg.show({\s+title: gettext(‘No valid sub)/void({ \/\/\1/g” /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy.service
```

## Atualizar tudo

```bash
sudo apt update && sudo apt upgrade && sudo apt dist-upgrade
```

## Ativar Windows

```bash
irm https://massgrave.dev/get | iex
```



