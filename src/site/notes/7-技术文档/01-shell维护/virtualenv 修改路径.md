---
{"dg-publish":true,"permalink":"/7/01-shell/virtualenv/","tags":["python","pip","venv"],"dg-note-properties":{"tags":["python","pip","venv"]}}
---



路径改明后，修改 `.venv/bin/pip`

``` python
#!/home/graphrag/.venv/bin/python
```


修改 `.venv/bin/activate`
``` bash
VIRTUAL_ENV='/home/graphrag/.venv'
```