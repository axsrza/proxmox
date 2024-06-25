## debian-12-nvidia

```bash
#cuDNN
sudo apt remove --purge cudnn
sudo apt autoremove
sudo apt autoclean

#CUDA
sudo apt remove --purge cuda
sudo apt autoremove
sudo apt autoclean

#Nvidia Driver
nvidia-smi

sudo apt remove --purge nvidia-driver-XXX
sudo apt autoremove
sudo apt autoclean
  
```
