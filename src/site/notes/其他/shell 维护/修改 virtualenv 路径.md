---
{"dg-publish":true,"permalink":"/其他/shell 维护/修改 virtualenv 路径/","tags":["python","pip","venv"]}
---


路径改明后，修改 `.venv/bin/pip`

``` python
#!/home/graphrag/.venv/bin/python
```


修改 `.venv/bin/activate`
``` bash
VIRTUAL_ENV='/home/graphrag/.venv'
```