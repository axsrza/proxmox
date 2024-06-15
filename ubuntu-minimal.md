## Após a instalação do Ubuntu Server Minimal

```bash
sudo passwd root #digite a senha criada na instalação
#defina uma senha para o root
sudo nano /etc/ssh/sshd_config #retire o # na frente da linha que consta PermitRootLogin prohibit-password e substitua por PermitRootLogin yes

sudo reboot

```
