---
{"dg-publish":true,"permalink":"/7-技术文档/05-文本/bjai.pro 增加 fastgpt知识库/","tags":["fastgpt"]}
---

# 1 fastgpt 新增 api key
`工作台` -> `应用` -> `发布渠道` -> `API 访问` -> `新建`
保存 api key 和 api 根地址


# 2 bjai 增加 key
`key 池管理` -> `新增`

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408191302538.png)

# 3 修改 bjai 的会员组
`会员管理` -> `会员组列表` -> 编辑对应的会员组

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408191304999.png)

`第三方对话模型` 中选择刚才新建的 key

# 4 修改 bjai 前端

ssh 进入 bjai.pro，编辑 `/usr/share/nginx/client/config/system.json`
在 `model` 字段中增加：
```json
    {
      "label": "英语",
      "value": "yingyu-gaozhong",
      "enable": true,
      "isCustomModel": true,
      "icon": "https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202407011423257.png",
      "suffix": ["课标-高中"],
      "maxTokens": 4000,
      "maxContextToken": 128000,
      "maxResponseToken": 4000
    },
```

# 5 bjai 中增加角色

`角色仓库管理` -> `角色仓库管理`

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408191343540.png)
