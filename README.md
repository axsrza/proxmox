
## Após Instalação (This script provides options for managing Proxmox VE repositories, including disabling the Enterprise Repo, adding or correcting PVE sources, enabling the No-Subscription Repo, adding the test Repo, disabling the subscription nag, updating Proxmox VE, and rebooting the system.)

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```

## Remover LOCAL-LVM via Interface Grafica e Adicionar Content em LOCAL

```bash
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root
```

## Remover mensagem de SUBSCRIPTION

```bash
sed -Ezi.bak "s/(Ext.Msg.show\(\{\s+title: gettext\('No valid sub)/void\(\{ \/\/\1/g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy.service
```

## Atualizar Tudo

```bash
apt update && apt upgrade && apt dist-upgrade
```

## Ativar Windows / Office via powershell

```bash
irm https://massgrave.dev/get | iex
```

## Formatar SSD via CMD

```bash
diskpart
list disk
select disk
clean
create partition primary
format fs=ntfs
```

## Scrcpy Wifi via powershell

```bash
.\adb tcpip 5555
.\adb connect 192.168.1.1:5555
.\scrcpy -s 192.168.1.1:5555
```

## ifconfig

```bash
apt install net-tools
ifconfig
```

## Debian 12 VM

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/debian-vm.sh)"

passwd root

sed -i -e 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' -e 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
ssh-keygen -A
systemctl restart sshd
```

## Debian 12 VM (Resize the Bootdisk (/dev/sda))

```bash
apt update
apt install -y parted

parted /dev/sda
resizepart 1
Fix/Ignore? Fix
Partition number? 1
Yes/No? Yes
End? [2146MB]? -0
(parted) quit
(reboot if not going further)
```

## PCI Passthrough RTX3060ti

```bash
nano /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on iommu=pt"
update-grub

nano /etc/modules
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
reboot
dmesg | grep -e DMAR -e IOMMU

echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf
echo "options kvm ignore_msrs=1 report_ignored_msrs=0" > /etc/modprobe.d/kvm.conf

echo "blacklist radeon" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nvidia" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nvidiafb" >> /etc/modprobe.d/blacklist.conf
echo "blacklist snd_hda_intel" >> /etc/modprobe.d/blacklist.conf

lspci
lspci -n -s 06:00
10de:2414,10de:2288
echo "options vfio-pci ids=10de:2414,10de:2288 disable_vga=1" > /etc/modprobe.d/vfio.conf
update-initramfs -u
reboot

```
