---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/docker 的 proxy 配置/","tags":["docker","proxy"]}
---


# 1 debian docker

```bash
mkdir -p /etc/systemd/system/docker.service.d
touch /etc/systemd/system/docker.service.d/proxy.conf

cat << EOF > /etc/systemd/system/docker.service.d/proxy.conf
[Service] 
Environment="HTTP_PROXY=http://sd:Stable@scripts.ibreakwall.me:18884"
Environment="HTTPS_PROXY=http://sd:Stable@scripts.ibreakwall.me:18884"
Environment="NO_PROXY=localhost,127.0.0.1"
EOF
  
systemctl daemon-reload && systemctl restart docker
```

# 2 alpine docker

编辑 `/etc/init.d/docker`，在 `start_pre()` 中加入如下内容：
``` bash
start_pre() {
	export http_proxy="http://sd:Stable@scripts.ibreakwall.me:18884"
    export https_proxy="http://sd:Stable@scripts.ibreakwall.me:18884"
    export no_proxy="localhost, 127.0.0.1, ::1"
}
```

# 3 docker build
``` bash
docker build . \
    --build-arg "HTTP_PROXY=http://sd:Stable@scripts.ibreakwall.me:18884" \
    --build-arg "HTTPS_PROXY=http://sd:Stable@scripts.ibreakwall.me:18884 \
    --build-arg "NO_PROXY=localhost,127.0.0.1" \
    -t your/image:tag
```

# 4 container
编辑 `~/.docker/config.json`
```json
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://sd:Stable@scripts.ibreakwall.me:18884",
     "httpsProxy": "http://sd:Stable@scripts.ibreakwall.me:18884",
     "noProxy": "localhost,127.0.0.1,"
   }
 }
}
```

# 5 docker-compose
```yaml
services:
  xinference:
    environment:
      - http_proxy=http://sd:Stable@scripts.ibreakwall.me:18884
      - https_proxy=http://sd:Stable@scripts.ibreakwall.me:18884
    dns:
      - 222.249.170.163
```

