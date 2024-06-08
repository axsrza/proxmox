## Remover nvidia-cuda-cdnn

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

## Instalar nvidia-cuda-cdnn

```bash
#Nvidia Driver
sudo apt update
sudo apt upgrade
sudo ubuntu-drivers devices

sudo apt install nvidia-driver-XXX

sudo reboot

#CUDA
#https://developer.nvidia.com/cuda-12-1-0-download-archive?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_network
cd /home/homelab
mkdir downloads
cd downloads

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.0-1_all.deb

sudo dpkg -i cuda-keyring_1.0-1_all.deb

sudo apt-get update
sudo apt list -a cuda

sudo apt install cuda=12.1.0-1

#CUDA variável de ambiente
sudo nano /etc/environment

#Final do PATH
:/usr/local/cuda/bin
#Segunda linha
LD_LIBRARY_PATH="/usr/local/cuda/lib64"

sudo reboot

#cuDNN
#https://developer.nvidia.com/cudnn-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_network

cd /home/homelab
mkdir downloads
cd downloads

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb

sudo dpkg -i cuda-keyring_1.1-1_all.deb

sudo apt-get update
sudo apt-get -y install cudnn

#cuDNN variável de ambiente
sudo nano /etc/environment

#Segunda linha
:/usr/lib/x86_64-linux-gnu

sudo reboot

```
