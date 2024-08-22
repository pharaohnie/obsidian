---
{"dg-publish":true,"permalink":"/其他/shell 维护/把 acme.sh 的证书转换为 pfx 格式/","tags":["证书"]}
---

```bash
openssl pkcs12 -export -out ibreakwall.me.pfx -inkey \*.ibreakwall.me.key -in \*.ibreakwall.me.cer -certfile ca.cer
```