---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/pve 增加 swap 空间/","tags":["pve"]}
---


``` bash
fallocate -l 32G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
swapon --show
echo "/swapfile none swap sw 0 0" >> /etc/fstab
```