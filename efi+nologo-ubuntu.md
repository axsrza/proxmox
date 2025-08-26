
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
