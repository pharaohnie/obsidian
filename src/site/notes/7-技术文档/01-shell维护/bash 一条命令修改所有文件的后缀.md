---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/bash 一条命令修改所有文件的后缀/","tags":["bash"]}
---


```bash
for file in *.md; do mv "$file" "${file%.md}.txt"; done
```

