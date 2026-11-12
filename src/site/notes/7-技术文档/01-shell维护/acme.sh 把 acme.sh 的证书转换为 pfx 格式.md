---
{"dg-publish":true,"permalink":"/7/01-shell/acme-sh-acme-sh-pfx/","tags":["证书"],"dg-note-properties":{"tags":["证书"]}}
---

```bash
openssl pkcs12 -export -out ibreakwall.me.pfx -inkey \*.ibreakwall.me.key -in \*.ibreakwall.me.cer -certfile ca.cer
```