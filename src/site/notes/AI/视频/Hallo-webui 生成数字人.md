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

或者用命令行启动
``` bash
python scripts/inference.py \
  --source_image szr.jpg \
  --driving_audio audio.wav \
  --output output/szr.mp4 \
  --setting_steps 100 \
  --setting_cfg 3.5 \
  --settings_seed 42 \
  --settings_fps 25 \
  --settings_motion_pose_scale 1 \
  --settings_motion_face_scale 1 \
  --settings_motion_lip_scale 1 \
  --settings_n_motion_frames 2 \
  --settings_n_sample_frames 16
```