# PROXMOX
## Management Network Configuration

![management_network_configuration](imagens/management_network_configuration.png)

## Aumentar o LOCAL storage do Proxmox e remover LOCAL-LVM

```bash
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root
