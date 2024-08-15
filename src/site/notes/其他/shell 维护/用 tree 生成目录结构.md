---
{"dg-publish":true,"permalink":"/其他/shell 维护/用 tree 生成目录结构/","tags":["bash","tree"]}
---


``` bash
tree  prepair_dataset/ -a -I "*.txt" -I "*.doc*" -I "*.pptx" -I "*.json*" -I ".venv" -I ".git*" -I ".env.example" -I "__pycache__" --dirsfirst -F

prepair_dataset//
├── book/
├── docs/
│   └── done/
├── output/
│   └── done/
├── saveChunk/
├── .env
├── make_dataset.py
├── office2txt.py
├── README
├── run.sh*
└── split.py

7 directories, 6 files
```