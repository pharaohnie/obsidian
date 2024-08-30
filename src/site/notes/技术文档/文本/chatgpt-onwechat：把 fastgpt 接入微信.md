---
{"dg-publish":true,"permalink":"/技术文档/文本/chatgpt-onwechat：把 fastgpt 接入微信/","tags":["fastgpt"]}
---

实现功能：
1. 接入 fastgpt
2. 支持语音直接提问
3. 可以用 azure 的 dalle3 画图
4. 由 fastgpt 完成不同知识库和普通 AI 的分流

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
      IMAGE_CREATE_PREFIX: '["画"]'
      CONVERSATION_MAX_TOKENS: 2000
      SPEECH_RECOGNITION: 'True'
      CHARACTER_DESC: '你是基于大语言模型的AI智能助手，旨在回答并解决人们的任何问题，并且可以使用多种语言与人交流。'
      GROUP_WELCOME_MSG: '欢迎进入 AI 测试群。'
      EXPIRES_IN_SECONDS: 3600
      USE_GLOBAL_PLUGIN_CONFIG: 'True'
      USE_LINKAI: 'False'
      LINKAI_API_KEY: ''
      LINKAI_APP_CODE: ''
      VOICE_TO_TEXT: 'azure'
      TEXT_TO_VOICE: 'azure'
      AZURE_VOICE_API_KEY: 'xxxx'
      AZURE_VOICE_REGION: 'japaneast'
      TEXT_TO_IMAGE: 'dall-e-3'
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
      - http_proxy=http://sd:Stable@scripts.ibreakwall.me:18884
      - https_proxy=http://sd:Stable@scripts.ibreakwall.me:18884
      - no_proxy=43.255.200.126,127.0.0.1,localhost
    dns:
      - 222.249.170.163
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

- 使用 azure 进行画图
- one-api 配置了代理，但是排除了43.255.200.126，这个 IP 就是 fastgpt 的 IP

```bash
docker-compose up -d && docker-compose logs -f
```

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408220935969.png)

用个人微信扫描二维码。`!!! 注意，个人微信必须绑定银行卡，否则会登录失败`

# 2 配置 one-api

在 one-api 中接入 fastgpt
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408221003786.png)


# 3 fastgpt 分流配置

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202408221439623.png)
