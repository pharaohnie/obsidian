---
{"dg-publish":true,"permalink":"/7-技术文档/01-shell维护/virtualenv 修改路径/","tags":["python","pip","venv"]}
---



路径改明后，修改 `.venv/bin/pip`

``` python
#!/home/graphrag/.venv/bin/python
```


修改 `.venv/bin/activate`
``` bash
VIRTUAL_ENV='/home/graphrag/.venv'
```