---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/使用国内源安装 docker/","tags":["docker"]}
---

```bash
apt install curl vim wget gnupg dpkg apt-transport-https lsb-release ca-certificates
curl -sS https://download.docker.com/linux/debian/gpg | gpg --dearmor > /usr/share/keyrings/docker-ce.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-ce.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian $(lsb_release -sc) stable" > /etc/apt/sources.list.d/docker.list
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin
wget -O /usr/local/bin/docker-compose "https://github.com/docker/compose/releases/download/v2.29.2/docker-compose-linux-x86_64"
chmod +x /usr/local/bin/docker-compose
```