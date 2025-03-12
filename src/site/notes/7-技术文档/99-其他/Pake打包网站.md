---
{"dg-publish":true,"permalink":"/7-技术文档/99-其他/Pake打包网站/"}
---


# 1 代理设置（先不用管）

`/root/iptables-tongshi-proxy.sh`

```bash
#!/bin/bash

iptables-save | grep 28888 | sed "s/^-A/iptables -D/g" | sh

# 云
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 43.255.200.0/24 -j ACCEPT

# 三小
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 123.117.61.168/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 124.127.217.168/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 103.213.165.171/32 -j ACCEPT

# 三小科技园
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 58.119.3.80/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 58.119.250.128/25 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 58.119.251.0/24 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 124.127.217.168/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 123.117.61.170/31 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 103.213.165.170/31 -j ACCEPT

# 海淀实验小学
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 103.213.165.24/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 123.117.61.24/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 124.127.217.24/29 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 103.213.168.38/32 -j ACCEPT

# 清华附中永丰
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 223.104.41.43/32 -j ACCEPT
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 124.127.216.241/32 -j ACCEPT

# 马冠龙
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 124.127.217.53/32 -j ACCEPT

# 丰台信息中心
iptables -A INPUT -i ens18 -p tcp --dport 28888 -s 103.239.194.0/24 -j ACCEPT

iptables -A INPUT -i ens18 -p tcp --dport 28888 -j DROP
```
`


# 2 pake打包



先安装windows版本的nodejs
再用npm安装pake-cli
```bash
npm install -g pake-cli
```
文档参照：[GitHub - tw93/Pake: 🤱🏻 Turn any webpage into a desktop app with Rust.  🤱🏻 利用 Rust 轻松构建轻量级多端桌面应用](https://github.com/tw93/Pake)

给cli.js打补丁


```js
    // inject js or css files
    if (inject?.length > 0) {
        // 处理逗号分隔的文件列表
        const injectArray = inject.includes(',') ? inject.split(',') : [inject];
        
        if (!injectArray.every(item => item.endsWith('.css') || item.endsWith('.js'))) {
            logger.error('The injected file must be in either CSS or JS format.');
            return;
        }
        const files = injectArray.map(filepath => (path.isAbsolute(filepath) ? filepath : path.join(process.cwd(), filepath)));
        tauriConf.pake.inject = files;
        await combineFiles(files, injectFilePath);
    }
    else {
        tauriConf.pake.inject = [];
        await fsExtra.writeFile(injectFilePath, '');
    }
```

制作icon，使用[recraft-ai/recraft-20b-svg – Run with an API on Replicate](https://replicate.com/recraft-ai/recraft-20b-svg)
提示词为`英文内容, simple, colorful`
style选择`icon`

把png转换为ico，[PNG to ICO \| CloudConvert](https://cloudconvert.com/png-to-ico)
有使用次数限制，自己去找其他的转换服务
记得设置为128x128


google教学小应用：[All Experiments - Experiments with Google](https://experiments.withgoogle.com/experiments)


pake_package.bat：打包程序
inject.css：负责屏蔽页面内某元素
inject.js：负责屏蔽鼠标右键，自动点击某按钮等


也可以用`pake_package.bat`，方便修改打包配置
```bash
@echo off

set URL=https://artsandculture.google.com/experiment/jwG3m7wQShZngw
set APP_NAME=TM-pose
set ICON_FILE=TM-pose.ico

pake %URL% --width 800 --height 600 --name %APP_NAME% --icon %ICON_FILE% ^
    --proxy-url http://scripts.ibreakwall.me:28888 --inject inject.css,inject.js ^
    --installer-language zh-CN --debug

```


使用`inject.css`屏蔽页面不需要的元素
注意，这个css只是示例，不同的网站需要不同的css规则
```css
#open-in-drive-button,
#open-file-button {
    display: none !important;
}

html body tm-hamburger-menu {
    display: none;
}
```


使用`inject.js`禁止右键
```js
document.addEventListener("contextmenu", function(event) {
    event.preventDefault();
});
```



# 3 inject.css调试

chrome安装[Custom CSS by Denis - Chrome 应用商店](https://chromewebstore.google.com/detail/custom-css-by-denis/cemphncflepgmgfhcdegkbkekifodacd?hl=zh-CN&utm_source=ext_sidebar)


右键需要屏蔽的元素，点击`检查`，选择`复制 selector`

![image.png|650](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202503120649240.png)

添加`display:none;`

```css
#root > main > div:nth-child(1) > div > div._footer_1y5ce_51 > button > span {display:none;}
```