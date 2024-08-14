---
{"dg-publish":true,"permalink":"/AI/文本/GraphRAG/","tags":["知识库","graphrag"]}
---

# 1 安装
```bash
mkdir graphrag
virtualenv .venv
source .venv/bin/activate
pip install graphrag
```


# 2 初始化文本库
``` bash
mkdir -p sanxiao/input
python -m graphrag.index --init --root ./sanxiao/
```
把需要放入知识库的内容复制到这个目录中

修改 `./sanxiao/setting.yaml` 中的几个内容
``` yaml
llm:
  api_key:  ${GRAPHRAG_LLM_API_KEY}
  model: ${GRAPHRAG_LLM_MODEL}
  api_base: ${GRAPHRAG_LLM_API_BASE}
  model_supports_json: false   # 如果模型支持 json 输出，改为 true
embeddings:
  llm:
    api_key: ${GRAPHRAG_EMBEDDING_API_KEY}
    model: ${GRAPHRAG_EMBEDDING_MODEL}
    api_base: ${GRAPHRAG_EMBEDDING_API_BASE}
claim_extraction:
	enabled: true
input:
  file_pattern: ".*\\.txt$".   # 要跟input目录中的文本扩展名一致
```



编辑 `.env`
``` text
GRAPHRAG_LLM_API_KEY=sk-xxx
GRAPHRAG_LLM_MODEL=gpt-4o-mini
GRAPHRAG_LLM_API_BASE=[Site Unreachable](https://api.openai.com/v1)


GRAPHRAG_EMBEDDING_API_KEY=sk-xxx
GRAPHRAG_EMBEDDING_MODEL=text-embedding-3-large
GRAPHRAG_EMBEDDING_API_BASE=https://api.openai.com/v1
```

# 3 自动优化提示词
``` bash
python -m graphrag.prompt_tune \
  --config ./sanxiao/settings.yaml --root ./sanxiao \
  --no-entity-types \
  --language Chinese \
  --output sanxiao/prompts
```

# 4 开始索引
``` bash
python -m graphrag.index --verbose --root ./sanxiao/
```