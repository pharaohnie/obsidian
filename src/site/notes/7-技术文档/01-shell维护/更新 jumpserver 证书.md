---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/更新 jumpserver 证书/","tags":["jumpserver","证书"]}
---

进入 `/opt/jumpserver/config/nginx/cert` 目录，备份之前的 `server.pem` 和 `server.key`

用 `acem.sh` 生成的 `fullchain.cer` 替换 `server.pem`
用 `acem.sh` 生成的 `ibreakwall.me.key` 替换 `server.key`

重启 jumpserver，`jmsctl stop && jmsctl start`