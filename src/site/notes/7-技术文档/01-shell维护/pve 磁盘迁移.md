---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/pve 磁盘迁移/","tags":["pve"]}
---

先找到vm在哪台pve上。
找到vm的id
找到磁盘的id，如scsi0
找到要迁移到哪个存储


```bash
qm move_disk <VMID> <DISK_ID> <TARGET_STORAGE>
```

例如
`qm move_disk 238 scsi0 synology-lvm`
