---
{"dg-publish":true,"permalink":"/AI/文本/Llama Factory 微调/","tags":["AI","微调","llama_factory"]}
---

# 1 准备数据集

## 1.1 选项 1：提取一般数据集

### 1.1.1 目录结构
``` text
prepair_dataset/
├── book
└── saveChunk
```

### 1.1.2 安装
``` bash
git clone https://github.com/pharaohnie/make_dataset
cd make_dataset
virtualenv .env
source .venv/bin/activate
pip install -r requirements.txt
```

### 1.1.3 切割文本
编辑 `.env`

``` text
# 需要切割的文本
INPUT_FILE_PATH=./book/zhenhuan.txt

BASE_URL=http://10.0.1.125:30004/v1
API_KEY=sk-xxx
MODEL=Qwen/Qwen2-72B-Instruct-AWQ

# 每个分段中提取问题的数量
ENTRIES_PER_FILE=20
```

把需要切割的长文本放到 `./book/` 中，这里命名为 `zhenhuan.txt`
运行 `python split.py`，切割后的文本后保存到 `./saveChunk/`，切割后的分段文本为 2048 个 token。

### 1.1.4 提取问题
运行 `python make_dataset.py`，从 `./saveChunk/` 中的 `.txt` 文件提取问题。
原理是：
``` text
基于以下文本，生成1个用于指令数据集的高质量条目。条目应该直接关联到给定的文本内容，提出相关的问题或任务。
请使用{instruction_type}作为提问引导词进行提问。下面是提问方式的举例：
```
 `{instruction_type}` 包含了：`如何看待`、`分析`、`比较`、`解释`、`评价`、`为什么` 几种提问方式。
运行完成后，会生成 `instruction_dataset.parquet`，这个文件是可以上传到 `huggingface` 的。
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408111625164.png)
在实际微调中，只有 `instructon` 和 `output` 有用




## 1.2 选项 2：提取某人对话

### 1.2.1 目录结构
``` text
get_role_dialogue/
├── dataset
└── output
```

### 1.2.2 安装
``` bash
git clone https://github.com/pharaohnie/get_role_dialogue
cd get_role_dialogue
virtualenv .env
source .venv/bin/activate
pip install -r requirements.txt
```

### 1.2.3 提取对话
编辑 `.env`
``` text
DEEPSEEK_API_KEY=sk-xxx

# 提取对话的文本，可以是剧本，可以是小说文本等
TEXTFILE=./dataset/zhenhuan.txt

#  要提取对话人物的名字、别名、小名儿
ROLES=皇帝,皇上,四郎
```

运行 `python main.py`，会生成 `./output/instruction_dataset.parquet`，这个文件也可以上传到 `huggingface`。
同时还会生成 `./output/zhenhuan.json` 和 `./output/zhenhuan.jsonl`
`!!! zhenhuan.jsonl 并不是标准格式，是一个临时的过程文件`



## 1.3 几种训练集的转换

### 1.3.1 格式区别

一共有三种训练集：`json`、`jsonl`、`parquet`
#### 1.3.1.1 json
``` json
[
  {
    "instruction": "",
    "input": "请皇上翻牌子。",
    "output": "哪来那么多话？"
  },
  {
    "instruction": "",
    "input": "皇上，太后来了。",
    "output": "快，请太后进来。"
  }
]
```

#### 1.3.1.2 jsonl
```
{"input": "徐进良", "output": "请皇上翻牌子。"}
{"role": "小厦子", "dialogue": "皇上，太后来了。"}
```

#### 1.3.1.3 paraquet

Parquet文件是一种列式存储格式，广泛用于大数据处理和分析。它有以下几个显著特点：

1. **列式存储**：Parquet文件以列为单位进行存储，而不是传统的行式存储。这使得它在处理某些特定列的查询时非常高效，因为只需要读取相关列的数据，而不必读取整个表的数据。
2. **压缩效率高**：由于数据在列内通常具有相似性，Parquet文件可以对每一列的数据进行高度压缩，从而减少存储空间和I/O操作的成本。常用的压缩算法包括Snappy、Gzip和LZO。
3. **灵活性和扩展性**：Parquet支持复杂的数据类型，包括嵌套结构（例如，嵌套的结构体和数组）。这使得Parquet能够很好地适应复杂的数据模型，适用于多种数据分析场景。
4. **高效的读写性能**：由于列式存储和压缩的特性，Parquet文件在读写性能上表现出色，特别是在处理大规模数据集时。对于只需要访问部分列的查询，Parquet可以显著减少I/O操作，从而提升查询速度。
5. **跨平台支持**：Parquet文件格式是由Apache开源的，并且得到了多个大数据处理平台的广泛支持，例如Apache Hadoop、Apache Spark、Apache Drill等。因此，Parquet文件可以在不同平台之间无缝传输和使用。
6. **自描述文件格式**：Parquet文件中包含了元数据，描述了文件的结构和存储模式，这使得文件在没有额外描述的情况下也能够被正确解读。这些元数据通常包括列的名称、类型、压缩方式等信息。
7. **类型丰富**：Parquet支持多种数据类型，包含基本数据类型（如整数、浮点数、布尔值等）和复杂数据类型（如数组、结构体等），这使得它在处理各种数据格式时具有很强的适应性。




# 2 安装 llama factory

``` bash
git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
virtualenv .venv
source .venv/bin/activate
pip install -e ".[torch,metrics,bitsandbytes,awq,vllm,qwen,modelscope]"
pip install oss2 addict
```


