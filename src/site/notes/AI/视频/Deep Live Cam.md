---
{"dg-publish":true,"permalink":"/AI/视频/Deep Live Cam/","tags":["数字人","AI"]}
---

# 1 基础安装

``` bash
conda create -n dlc python==3.10 -y
conda activate dlc
git clone https://github.com/hacksider/Deep-Live-Cam.git
cd Deep-Live-Cam/models
curl -O -L https://huggingface.co/hacksider/deep-live-cam/resolve/main/GFPGANv1.4.pth
curl -O -L https://huggingface.co/hacksider/deep-live-cam/resolve/main/inswapper_128_fp16.onnx
cd ..
```

# 2 Linux 电脑
```
pip install -r requirements.txt
pip uninstall onnxruntime onnxruntime-gpu
pip install onnxruntime-gpu==1.16.3
python run.py --execution-provider cuda
```

# 3 Mac 电脑

修改 `modules/ui.py`
找到 `cap = cv2.VideoCapture(0)`，把 `0` 改为 mac 摄像头的序号

``` bash
pip install --use-pep517 basicsr
pip install -r requirements.txt
pip uninstall onnxruntime onnxruntime-silicon
pip install onnxruntime-silicon==1.13.1
python run.py --execution-provider coreml
```
