---
{"dg-publish":true,"permalink":"/7-技术文档/10-图像、视频/PhotoMaker 人像风格迁移/","tags":["数字人"]}
---


不用训练人物 lora，直接用一张图片生成不同风格的照片

# 1 安装
```bash
conda create --name photomaker python=3.10 -y
conda activate photomaker
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 cudnn=8.1.0 -c pytorch -c nvidia -y
pip install -U pip
git clone https://github.com/TencentARC/PhotoMaker
cd PhotoMaker
pip install -r requirements.txt
pip install git+https://github.com/TencentARC/PhotoMaker.git
pip install einops onnxruntime-gpu
```

编辑 `/root/miniconda3/envs/photomaker/lib/python3.10/site-packages/photomaker/pipeline_t2i_adapter.py`
注释掉 `resume_download=resume_download,`，如下：
```python
if not isinstance(pretrained_model_name_or_path_or_dict, dict):
    model_file = _get_model_file(
        pretrained_model_name_or_path_or_dict,
        weights_name=weight_name,
        cache_dir=cache_dir,
        force_download=force_download,
        # resume_download=resume_download,
        proxies=proxies,
        local_files_only=local_files_only,
        token=token,
        revision=revision,
        subfolder=subfolder,
        user_agent=user_agent,
    )
```

修改 `gradio_demo/app_v2.py`，最后一行改为 `demo.launch(server_name="0.0.0.0")`
# 2 运行

run.sh

``` bash
#!/bin/bash
export CUDA_VISIBLE_DEVICES=0

source /root/miniconda3/bin/activate photomaker
cd /home/PhotoMaker
python gradio_demo/app_v2.py
```
