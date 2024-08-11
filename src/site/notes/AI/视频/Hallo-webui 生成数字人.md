---
{"dg-publish":true,"permalink":"/AI/视频/Hallo-webui 生成数字人/","tags":["数字人"]}
---


``` bash
git clone https://github.com/daswer123/hallo-webui
cd hallo-webui
git clone https://huggingface.co/fudan-generative-ai/hallo pretrained_models
wget -O pretrained_models/hallo/net.pth https://huggingface.co/fudan-generative-ai/hallo/resolve/main/hallo/net.pth?download=true
virtualenv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install -e .
pip install torch==2.2.2+cu121 torchaudio torchvision --index-url https://download.pytorch.org/whl/cu121
pip install onnxruntime-gpu
```

如果希望在 `0.0.0.0` 上监听，修改 `app.py` 的最后一行
``` python
    demo.launch(inbrowser=True, share=share_url)
```
为
``` python
    demo.launch(inbrowser=True, share=share_url, server_name="0.0.0.0")
```

运行 `python app.py`