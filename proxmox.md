## HOST - Após Instalação

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"

```

## HOST - Remover LOCAL-LVM via Interface Grafica e Adicionar Content em LOCAL

```bash
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root

```

## HOST - PCI Passthrough RTX3060ti

```bash
nano /etc/default/grub
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"
update-grub

nano /etc/modules
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd

echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf
echo "options kvm ignore_msrs=1 report_ignored_msrs=0" > /etc/modprobe.d/kvm.conf
echo "blacklist nvidia" >> /etc/modprobe.d/blacklist.conf

lspci
lspci -n -s 06:00
10de:2414,10de:2288
echo "options vfio-pci ids=10de:2414,10de:2288 disable_vga=1" > /etc/modprobe.d/vfio.conf
update-initramfs -u
dmesg | grep -e DMAR -e IOMMU
reboot

```

## HOST - IOMMU script

```bash
nano /root/iommu_group.sh
###### iommu script ######
#!/bin/bash
shopt -s nullglob
for g in $(find /sys/kernel/iommu_groups/* -maxdepth 0 -type d | sort -V); do
    echo "IOMMU Group ${g##*/}:"
    for d in $g/devices/*; do
        echo -e "\t$(lspci -nns ${d##*/})"
    done;
done;
###### iommu script ######
chmod +x /root/iommu_group.sh
/root/iommu_group.sh

```

## VM - Debian 12 script

```bash
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/debian-vm.sh)"
passwd root
sed -i -e 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/g' -e 's/^PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
ssh-keygen -A
systemctl restart sshd

```

## VM - Debian 12 (Resize the Bootdisk (/dev/sda))

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
reboot

```

## VM - Instalar Driver Nvidia

```bash
nano /etc/initramfs-tools/modules
vfio-pci
update-initramfs -u

echo 'deb http://deb http://deb.debian.org/debian bookworm main non-free-firmware' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm main non-free-firmware' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian-security/ bookworm-security main non-free-firmware' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian-security/ bookworm-security main non-free-firmware' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian bookworm-updates main non-free-firmware' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm-updates main non-free-firmware' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian bookworm main' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm main' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian-security/ bookworm-security main' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian-security/ bookworm-security main' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian bookworm-updates main' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm-updates main' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian bookworm main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian-security/ bookworm-security main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian-security/ bookworm-security main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian bookworm-updates main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm-updates main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb http://deb.debian.org/debian bookworm-backports main contrib non-free' | tee -a /etc/apt/sources.list
echo 'deb-src http://deb.debian.org/debian bookworm-backports main contrib non-free' | tee -a /etc/apt/sources.list
apt update && apt upgrade -y
apt install nvidia-driver
reboot

lspci -k | grep -EA3 'VGA|3D|Display'
nvidia-settings -c :0 -a "[gpu:0]/GPUFanControlState=1"

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
apt update
apt install net-tools
ifconfig

```

## adicionar hd externo lxc

```bash
cd /etc/pve/lxc/
ls

nano 100

mp0: /mnt/pve/hdexterno,mp=/mnt/
mp1: /mnt/pve/hdexterno2,mp=/mnt2/

```
