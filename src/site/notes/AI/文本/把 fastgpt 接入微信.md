---
{"dg-publish":true,"permalink":"/AI/文本/把 fastgpt 接入微信/","tags":["fastgpt"]}
---

# 1 部署 [chatgpt-on-wechat](https://github.com/zhayujie/chatgpt-on-wechat)

```yaml
version: '3.4'
services:
  chatgpt-on-wechat:
    image: zhayujie/chatgpt-on-wechat
    container_name: chatgpt-on-wechat
    hostname: wechat
    security_opt:
      - seccomp:unconfined
    environment:
      OPEN_AI_API_KEY: 'sk-xxxx'
      OPEN_AI_API_BASE: 'http://oneapi:3000/v1'
      MODEL: 'gpt-4o'
      PROXY: ''
      SINGLE_CHAT_PREFIX: '[""]'
      SINGLE_CHAT_REPLY_PREFIX: '""'
      GROUP_CHAT_PREFIX: '["@智能助手"]'
      GROUP_NAME_WHITE_LIST: '["智能助手"]'
      IMAGE_CREATE_PREFIX: '["画", "看", "找"]'
      CONVERSATION_MAX_TOKENS: 8000
      SPEECH_RECOGNITION: 'False'
      CHARACTER_DESC: '你是基于大语言模型的AI智能助手，旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。'
      GROUP_WELCOME_MSG: '欢迎进入 AI 测试群。提问时请"@智能助手"。您可以问与项目申报、政策文件、等级保护、法律法规、智慧校园方案相关的问题，例如“@智能助手 等级保护分为几级？”。如需联网搜索，请直接输入“@智能助手 搜索：您的问题”。'
      EXPIRES_IN_SECONDS: 3600
      USE_GLOBAL_PLUGIN_CONFIG: 'True'
      USE_LINKAI: 'False'
      LINKAI_API_KEY: ''
      LINKAI_APP_CODE: ''
    networks:
      - wechat

  one-api:
    image: justsong/one-api:latest
    hostname: oneapi
    restart: always
    command: --log-dir /app/logs
    volumes:
      - oneapi:/data
      - logs:/app/logs
    ports:
      - 30002:3000
    environment:
      - SQL_DSN=oneapi:jszczx@tcp(db:3306)/one-api  # 修改此行，或注释掉以使用 SQLite 作为数据库
      - REDIS_CONN_STRING=redis://redis
      - SESSION_SECRET=random_string  # 修改为随机字符串
      - TZ=Asia/Shanghai
    depends_on:
      - redis
      - db
    healthcheck:
      test: [ "CMD-SHELL", "wget -q -O - http://localhost:3000/api/status | grep -o '\"success\":\\s*true' | awk -F: '{print $2}'" ]
      interval: 30s
      timeout: 10s
      retries: 3
    networks:
      - wechat

  redis:
    image: redis:latest
    restart: always
    networks:
      - wechat

  db:
    image: mysql:8.2.0
    restart: always
    volumes:
      - mysql:/var/lib/mysql  # 挂载目录，持久化存储
    environment:
      TZ: Asia/Shanghai   # 设置时区
      MYSQL_ROOT_PASSWORD: 'jszczx' # 设置 root 用户的密码
      MYSQL_USER: oneapi   # 创建专用用户
      MYSQL_PASSWORD: 'jszczx'    # 设置专用用户密码
      MYSQL_DATABASE: one-api   # 自动创建数据库
    networks:
      - wechat

volumes:
  oneapi:
  logs:
  mysql:

networks:
  wechat:
```

```bash
docker-compose up -d && docker-compose logs -f
```

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408220935969.png)

用个人微信扫描二维码。`!!! 注意，个人微信必须绑定银行卡，否则会登录失败`

# 2 配置 one-api

在 one-api 中接入 fastgpt
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408221003786.png)
