---
{"dg-publish":true,"permalink":"/AI/文本/MinerU 文字识别/","tags":["ocr"]}
---

[GitHub - opendatalab/MinerU: A one-stop, open-source, high-quality data extraction tool, supports PDF/webpage/e-book extraction.一站式开源高质量数据提取工具，支持PDF/网页/多格式电子书提取。](https://github.com/opendatalab/MinerU)

`!!! 表格是用 latex 渲染的，有问题。`
# 1 安装

``` bash
conda create -n MinerU python=3.10 -y
conda activate MinerU
pip install magic-pdf[full]==0.7.0b1 --extra-index-url https://wheels.myhloli.com -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install paddlepaddle-gpu==3.0.0b1 -i https://www.paddlepaddle.org.cn/packages/stable/cu118/

mkdir MinerU
cd MinerU
git lfs clone https://huggingface.co/wanderkid/PDF-Extract-Kit


cat <<EOF > /root/magic-pdf.json
{
    "bucket_info":{
        "bucket-name-1":["ak", "sk", "endpoint"],
        "bucket-name-2":["ak", "sk", "endpoint"]
    },
    "temp-output-dir":"/home/MinerU/output",
    "models-dir":"/home/MinerU/PDF-Extract-Kit/models",
    "device-mode":"cuda",
    "table-config": {
        "is_table_recog_enable": true,
        "max_time": 4000
    }
}
EOF
```

# 2 运行
`magic-pdf -p test.pdf -o output -m auto`