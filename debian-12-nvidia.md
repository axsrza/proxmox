## debian-12-nvidia

```bash
sudo apt install nvidia-detect linux-headers-amd64
sudo apt install nvidia-driver firmware-misc-nonfree

wget https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo add-apt-repository contrib
sudo apt-get update
sudo apt list -a cuda

sudo apt install cuda=12.1.0-1

wget https://developer.download.nvidia.com/compute/cuda/repos/debian12/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo sed -i "s|^Components: main$|& contrib|" /etc/apt/sources.list.d/debian.sources
sudo apt-get update
sudo apt-get -y install cudnn

```
