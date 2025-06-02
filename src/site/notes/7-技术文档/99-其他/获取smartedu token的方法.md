---
{"dg-publish":true,"permalink":"/7-技术文档/99-其他/获取smartedu token的方法/"}
---


下载：https://github.com/alterem/knowledge-grab/releases/download/v1.0.1/KnowledgeGrab_1.0.0_x64_en-US.msi



1. **打开浏览器**，访问[国家中小学智慧教育平台](https://auth.smartedu.cn/uias/login)并**登录账号**。
2. 按下 **F12** 或 **Ctrl+Shift+I**，或右键——检查（审查元素）打开**开发者工具**，选择**控制台（Console）**。
3. 在控制台粘贴以下代码后回车（Enter）：


```js
(function() {
  const authKey = Object.keys(localStorage).find(key => key.startsWith("ND_UC_AUTH"));
  if (!authKey) {
    console.error("未找到 Access Token，请确保已登录！");
    return;
  }
  const tokenData = JSON.parse(localStorage.getItem(authKey));
  const accessToken = JSON.parse(tokenData.value).access_token;
  console.log("%cAccess Token:", "color: green; font-weight: bold", accessToken);
})();
```


![image.png|650](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202506021646378.png)

把token填入到KnowledgeGrab中

![image.png|650|650](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202506021647848.png)