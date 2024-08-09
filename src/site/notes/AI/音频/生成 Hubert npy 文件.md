---
{"dg-publish":true,"permalink":"/ai//hubert-npy/","tags":["AI","语音","数字人","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry","gardenEntry"]}
---

> npy 文件可用于 anitalker 的数字人生成
# 1 安装
	
```bash
git clone https://github.com/newgenai79/talking_face_preprocessing_windows
cd talking_face_preprocessing_windows
conda create -n tfpw python==3.9.0
conda activate tfpw
pip install -r requirements.txt
mkdir weights
cd weights
git clone https://huggingface.co/TencentGameMate/chinese-hubert-large
```

# 2 prepare_npy.sh
prepare_npy.sh，放到 `talking_face_preprocessing_windows` 的根目录下
``` bash
#!/bin/bash
source /root/miniconda3/bin/activate tfpw

INPUT="audio.wav"

if [ ! -d "./data/input" ]; then
  mkdir -p ./data/input
else
  rm ./data/input/*
fi

if [ ! -d "./data/output" ]; then
  mkdir -p ./data/output
else
  rm ./data/output/*
fi

ffmpeg -y -i ./data/${INPUT} -t 00:05:00 -ar 16000 ./data/input/audio.wav

CUDA_VISIBLE_DEVICES=0 python extract_audio_features.py \
  --model_path "weights/chinese-hubert-large" \
  --audio_dir_path "./data/input/" \
  --audio_feature_saved_path "./data/output/" \
  --computed_device "cuda" \
  --padding_to_align_audio True
```
# 3 生成 npy 文件

单独的一个 wav 音频文件放到 `./data` 下，改名为 `audio.wav`
然后运行 `prepare_npy.sh`，之后会在 `./data/output/` 生成一个 `audio.npy`

