---
{"dg-publish":true,"permalink":"/7/01-shell/pve-swap/","tags":["pve"],"dg-note-properties":{"tags":["pve"]}}
---


``` bash
fallocate -l 32G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
swapon --show
echo "/swapfile none swap sw 0 0" >> /etc/fstab
```