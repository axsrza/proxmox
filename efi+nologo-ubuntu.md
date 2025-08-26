
## 

```
sudo apt install os-prober
```

## 

```
sudo nano /etc/default/grub
```

## 

```
GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=menu
GRUB_TIMEOUT=10
GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_CMDLINE_LINUX=""
GRUB_DISABLE_OS_PROBER=false
GRUB_GFXMODE=2560x1080
GRUB_GFXPAYLOAD_LINUX=keep
```

## 

```
sudo update-grub
```

## 

```
sudo apt remove plymouth plymouth-theme-ubuntu-text plymouth-theme-spinner
```

## 

```
sudo apt autoremove
```

## 

```
sudo reboot
```
