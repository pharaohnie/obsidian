---
{"dg-publish":true,"permalink":"/AI/图像、视频/DH_Live 数字人/","tags":["数字人"]}
---

[GitHub - kleinlee/DH\_live: 每个人都能用的数字人](https://github.com/kleinlee/DH_live)
# 1 安装
```bash
conda create --name dh_live python=3.11 -y
conda activate dh_live
pip install torch==2.1.1+cu121 torchvision==0.16.1+cu121 -f https://download.pytorch.org/whl/torch_stable.html
pip install --upgrade pip
pip install -r requirements.txt
pip install gradio gfpgan moviepy
```