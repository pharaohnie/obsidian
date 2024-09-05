---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/docker 安装/","tags":["docker"]}
---


```bash
export http_proxy="[http://sd:Stable@scripts.ibreakwall.me:18884](http://sd:Stable@scripts.ibreakwall.me:18884)"
export https_proxy="[http://sd:Stable@scripts.ibreakwall.me:18884](http://sd:Stable@scripts.ibreakwall.me:18884)"
export no_proxy="localhost, 127.0.0.1, ::1"
apt-get update
apt-get install ca-certificates curl gnupg sudo -y
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys xxxxx
apt-get clean && rm -rf /var/lib/apt/lists/* && apt update
apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
wget -O /usr/local/bin/docker-compose "https://github.com/docker/compose/releases/download/v2.29.2/docker-compose-linux-x86_64"
chmod +x /usr/local/bin/docker-compose

```