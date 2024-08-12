---
{"dg-publish":true,"permalink":"/AI/视频/Anitaker 生成数字人/","tags":["AI","数字人"]}
---


[GitHub - X-LANCE/AniTalker: \[ACM MM 2024\] This is the official code for "AniTalker: Animate Vivid and Diverse Talking Faces through Identity-Decoupled Facial Motion Encoding"](https://github.com/X-LANCE/AniTalker)

效果没有 [[AI/视频/Hallo-webui 生成数字人\|Hallo]] 好

# 1 安装 antitalker

``` bash
conda create -n anitalker python==3.9.0
conda activate anitalker
conda install pytorch==1.8.0 torchvision==0.9.0 torchaudio==0.8.0 cudatoolkit=11.1 -c pytorch -c conda-forge
cd /home/
git clone https://github.com/X-LANCE/AniTalker
cd anitalker
pip install -r requirements.txt
mkdir ckpts
cd ckpts
git clone https://huggingface.co/taocode/anitalker_ckpts
pip install facexlib
pip install tb-nightly -i https://mirrors.aliyun.com/pypi/simple
pip install gfpgan
```


# 2 安装 talking_face_preprocessing_windows
用于生成 npy 文件

```bash
cd /home/
git clone https://github.com/newgenai79/talking_face_preprocessing_windows
cd talking_face_preprocessing_windows
conda create -n tfpw python==3.9.0
conda activate tfpw
pip install -r requirements.txt
mkdir weights
cd weights
git clone https://huggingface.co/TencentGameMate/chinese-hubert-large
```

# 3 生成 npy 文件

找一个 wav 文件，或者用 [[AI/音频/CosyVoice 训练\|CosyVoice]] 生成语音文件，改名为 `audio.wav`

prepare_npy.sh，放到 `talking_face_preprocessing_windows` 的根目录下
``` bash
#!/bin/bash
cd /home/talking_face_preprocessing_windows
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

ffmpeg -hide_banner -loglevel error -y -i ./data/${INPUT} -t 00:05:00 -ar 16000 ./data/input/audio.wav

CUDA_VISIBLE_DEVICES=0 python extract_audio_features.py \
  --model_path "weights/chinese-hubert-large" \
  --audio_dir_path "./data/input/" \
  --audio_feature_saved_path "./data/output/" \
  --computed_device "cuda" \
  --padding_to_align_audio True
```

把单独的一个 wav 音频文件放到 `./data` 下，改名为 `audio.wav`
然后运行 `prepare_npy.sh`，之后会在 `./data/output/` 生成一个 `audio.npy`


# 4 合成视频

设置好 `test_image_path`、`test_audio_path`、`test_hubert_path`
`--face_sr` 可以让生成的视频分辨率提高到 512x512
对比之下，感觉 `hubert_pose_only` 的效果会好一些。


``` bash
python ./code/demo.py \
    --infer_type 'hubert_pose_only' \
    --stage1_checkpoint_path 'ckpts/stage1.ckpt' \
    --stage2_checkpoint_path 'ckpts/stage2_pose_only_hubert.ckpt' \
    --test_image_path 'silang.jpg' \
    --test_audio_path 'silang.wav' \
    --test_hubert_path 'silang.npy' \
    --result_path './outputs/silang' --face_sr
```


或者使用 `run.sh` 脚本。`./data` 下放这两个文件
``` text
data/
├── audio.wav
└── szr.jpg
```

然后运行 `run.sh`

``` bash
#!/bin/bash

cp /home/AniTalker/data/audio.wav /home/talking_face_preprocessing_windows/data/
/home/talking_face_preprocessing_windows/prepare_npy.sh
cp /home/talking_face_preprocessing_windows/data/output/audio.npy ./data

cd /home/AniTalker
source /root/miniconda3/bin/activate anitalker

python ./code/demo.py \
    --infer_type 'hubert_pose_only' \
    --stage1_checkpoint_path 'ckpts/stage1.ckpt' \
    --stage2_checkpoint_path 'ckpts/stage2_pose_only_hubert.ckpt' \
    --test_image_path './data/szr.jpg' \
    --test_audio_path './data/audio.wav' \
    --test_hubert_path './data/audio.npy' \
    --result_path './data/' --face_sr
```