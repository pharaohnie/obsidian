---
{"dg-publish":true,"permalink":"/7/01-shell/bash/","tags":["bash"],"dg-note-properties":{"tags":["bash"]}}
---


```bash
for file in *.md; do mv "$file" "${file%.md}.txt"; done
```

