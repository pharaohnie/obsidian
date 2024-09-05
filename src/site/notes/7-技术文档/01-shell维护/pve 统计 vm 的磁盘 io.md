---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/pve 统计 vm 的磁盘 io/","tags":["pve"]}
---

```bash
#!/bin/bash

# 获取所有虚拟机的列表
vms=$(qm list | awk 'NR>1 {print $1}')

echo "VM Name, VM ID, DiskRead IO, DiskWrite IO"

for vmid in $vms; do
    # 获取虚拟机的名称
    vmname=$(qm list | awk -v id=$vmid '$1 == id {print $2}')

    # 获取初始的diskread和diskwrite值
    initial_read=$(qm status $vmid --verbose | grep diskread | awk '{print $2}')
    initial_write=$(qm status $vmid --verbose | grep diskwrite | awk '{print $2}')

    # 等待10秒
    sleep 10

    # 获取10秒后的diskread和diskwrite值
    final_read=$(qm status $vmid --verbose | grep diskread | awk '{print $2}')
    final_write=$(qm status $vmid --verbose | grep diskwrite | awk '{print $2}')

    # 计算10秒内的IO数量
    read_io=$(($final_read - $initial_read))
    write_io=$(($final_write - $initial_write))

    # 输出结果
    echo "$vmname, $vmid, $read_io, $write_io"
done
```