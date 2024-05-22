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
apt update && apt upgrade && apt dist-upgrade
```

## Ativar Windows / Office (powershell)

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

## Scrcpy Wifi (powershell)

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
lspci | grep VGA
06:00.0 VGA compatible controller: NVIDIA Corporation GA103 [GeForce RTX 3060 Ti] (rev a1)

dmesg | grep -e DMAR -e IOMMU
[    0.378204] pci 0000:00:00.2: AMD-Vi: IOMMU performance counters supported
[    0.381636] perf/amd_iommu: Detected AMD IOMMU #0 (2 banks, 4 counters/bank).



```
