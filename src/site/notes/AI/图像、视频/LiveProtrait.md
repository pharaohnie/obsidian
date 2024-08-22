---
{"dg-publish":true,"permalink":"/AI/图像、视频/LiveProtrait/","tags":["数字人"]}
---

# 1 安装
```bash
git clone https://github.com/KwaiVGI/LivePortrait
cd LivePortrait
conda create -n LivePortrait python=3.9 -y
conda activate LivePortrait
pip install torch==2.3.0 torchvision==0.18.0 torchaudio==2.3.0 --index-url https://download.pytorch.org/whl/cu121
pip install -r requirements.txt
huggingface-cli download KwaiVGI/LivePortrait --local-dir pretrained_weights --exclude "*.git*" "README.md" "docs"
```


```bash
python app.py --server-port 50000
```