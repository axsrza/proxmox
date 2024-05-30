## Expandir 100% do ssd

```bash
lsblk
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/ubuntu-vg/ubuntu-lv
sudo reboot

```
