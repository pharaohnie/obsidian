---
{"dg-publish":true,"permalink":"/技术文档/图像、视频/stable diffusion webui forge/","tags":["sd-webui"]}
---


```bash
conda create --name sd python=3.10 -y
conda activate sd
git clone https://github.com/lllyasviel/stable-diffusion-webui-forge
cd stable-diffusion-webui-forge
sed -i -e "s/^can_run_as_root=0/can_run_as_root=1/g" webui.sh
```


run.sh
```bash
#!/bin/bash
export CUDA_VISIBLE_DEVICES=0

source /root/miniconda3/bin/activate sd
cd /home/stable-diffusion-webui-forge

./webui.sh --port 30002
```