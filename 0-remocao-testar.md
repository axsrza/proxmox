
```bash
sudo systemctl disable \
  bluetooth.service \
  networking.service
```

```bash
sudo apt remove --purge bluetooth ifupdown wpasupplicant -y
```

```bash
sudo systemctl reset-failed
```

```bash
sudo systemctl disable \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service && \
sudo systemctl mask \
  wpa_supplicant.service wpa_supplicant@.service \
  wpa_supplicant-nl80211@.service wpa_supplicant-wired@.service
```

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo journalctl --rotate
sudo journalctl --vacuum-time=1d
```

```bash
sudo apt autoremove -y && sudo apt autoclean
sudo reboot
```
