---
{"dg-publish":true,"permalink":"/7-技术文档/05-文本/elasticsearch操作/"}
---



创建角色delete_sanxiao_role
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

删除权限
```bash
curl -u elastic:wjht2024 -X DELETE "http://43.255.200.154:9200/_security/role/delete_sanxiao_role"
```

创建用户sanxiao
```bash
curl -u elastic:wjht2024 -X POST "http://43.255.200.154:9200/_security/user/sanxiao" -H "Content-Type: application/json" -d '{
  "password" : "sanxiao",
  "roles" : [ "delete_sanxiao_role" ],
  "full_name" : "sanxiao",
  "email" : "sanxiao@sanxiao.com"
}'
```

重新设置密码
```bash
curl -u elastic:wjht2024 -X PUT "http://43.255.200.154:9200/_security/user/sanxiao/_password" -H "Content-Type: application/json" -d '{
  "password": "sanxiao"
}'
```

删除账号
```bash
curl -u elastic:wjht2024 -X DELETE "http://43.255.200.154:9200/_security/user/sanxiao"
```


清空sanxiao索引的命令
```bash
curl -u sanxiao:sanxiao -X POST "http://43.255.200.154:9200/sanxiao/_delete_by_query" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  }
}'
```


查看所有记录
```bash
curl -u sanxiao:sanxiao -X GET "http://43.255.200.154:9200/sanxiao/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  },
  "size": 1000
}'
```
