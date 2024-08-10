---
{"dg-publish":true,"permalink":"/AI/MinerU 文字识别/","tags":["ocr"]}
---

[GitHub - opendatalab/MinerU: A one-stop, open-source, high-quality data extraction tool, supports PDF/webpage/e-book extraction.一站式开源高质量数据提取工具，支持PDF/网页/多格式电子书提取。](https://github.com/opendatalab/MinerU)

``` bash
conda create -n MinerU python=3.10 -y
conda activate MinerU
pip install magic-pdf[full]==0.7.0b1 --extra-index-url https://wheels.myhloli.com -i https://pypi.tuna.tsinghua.edu.cn/simple

mkdir MinerU
cd MinerU
git lfs clone https://huggingface.co/wanderkid/PDF-Extract-Kit
```