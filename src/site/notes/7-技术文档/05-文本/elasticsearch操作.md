---
{"dg-publish":true,"permalink":"/7/05/elasticsearch/","dg-note-properties":{}}
---



# 1 创建角色delete_sanxiao_role
```bash
curl -u elastic:wjht2024 -X POST "http://43.255.200.154:9200/_security/role/delete_sanxiao_role" -H "Content-Type: application/json" -d '{
  "indices": [
    {
      "names": [ "sanxiao" ],
      "privileges": [ "read", "delete" ]
    }
  ]
}'
```

# 2 删除权限
```bash
curl -u elastic:wjht2024 -X DELETE "http://43.255.200.154:9200/_security/role/delete_sanxiao_role"
```

# 3 创建用户sanxiao
```bash
curl -u elastic:wjht2024 -X POST "http://43.255.200.154:9200/_security/user/sanxiao" -H "Content-Type: application/json" -d '{
  "password" : "sanxiao",
  "roles" : [ "delete_sanxiao_role" ],
  "full_name" : "sanxiao",
  "email" : "sanxiao@sanxiao.com"
}'
```

# 4 重新设置密码
```bash
curl -u elastic:wjht2024 -X PUT "http://43.255.200.154:9200/_security/user/sanxiao/_password" -H "Content-Type: application/json" -d '{
  "password": "sanxiao"
}'
```

# 5 删除账号
```bash
curl -u elastic:wjht2024 -X DELETE "http://43.255.200.154:9200/_security/user/sanxiao"
```


# 6 清空sanxiao索引的命令
```bash
curl -u sanxiao:sanxiao -X POST "http://43.255.200.154:9200/sanxiao/_delete_by_query" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  }
}'
```


# 7 查看所有记录
```bash
curl -u sanxiao:sanxiao -X GET "http://43.255.200.154:9200/sanxiao/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  },
  "size": 1000
}'
```


# 8 删除sanxiao索引

```bash
curl -u sanxiao:sanxiao -X DELETE "http://43.255.200.154:9200/sanxiao"
```