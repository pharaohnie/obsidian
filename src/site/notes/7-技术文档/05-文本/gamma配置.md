---
{"dg-publish":true,"permalink":"/7-技术文档/05-文本/gamma配置/"}
---


# 1 组件
guacamole：负责远程桌面的管理
xrdp：远程桌面
openbox：xwindow管理器
fcitx5：中文输入
firefox-esr：浏览器



# 2 xrdp
## 2.1 /etc/xrdp/xrdp.ini
```ini
[Globals]
ini_version=1
fork=true
port=33389
use_vsock=false
tcp_nodelay=true
tcp_keepalive=true
security_layer=negotiate
crypt_level=high
certificate=
key_file=
ssl_protocols=TLSv1.2, TLSv1.3
autorun=
allow_channels=true
allow_multimon=true
bitmap_cache=true
bitmap_compression=true
bulk_compression=true
max_bpp=16
new_cursors=true
use_fastpath=both
blue=009cb5
grey=dedede
ls_top_window_bg_color=009cb5
ls_width=350
ls_height=430
ls_bg_color=dedede
ls_logo_filename=
ls_logo_x_pos=55
ls_logo_y_pos=50
ls_label_x_pos=30
ls_label_width=65
ls_input_x_pos=110
ls_input_width=210
ls_input_y_pos=220
ls_btn_ok_x_pos=142
ls_btn_ok_y_pos=370
ls_btn_ok_width=85
ls_btn_ok_height=30
ls_btn_cancel_x_pos=237
ls_btn_cancel_y_pos=370
ls_btn_cancel_width=85
ls_btn_cancel_height=30

[Logging]
LogFile=xrdp.log
LogLevel=INFO
EnableSyslog=true

[LoggingPerLogger]

[Channels]
rdpdr=true
rdpsnd=true
drdynvc=true
cliprdr=true
rail=true
xrdpvr=true
tcutils=true

[Xorg]
name=Xorg
lib=libxup.so
username=ask
password=ask
ip=127.0.0.1
port=-1
code=20

[Xvnc]
name=Xvnc
lib=libvnc.so
username=ask
password=ask
ip=127.0.0.1
port=-1

[vnc-any]
name=vnc-any
lib=libvnc.so
ip=ask
port=ask5900
username=na
password=ask
[neutrinordp-any]

name=neutrinordp-any
lib=libxrdpneutrinordp.so
ip=ask
port=ask3389
username=ask
password=ask
```

## 2.2 /etc/xrdp/sesman.ini
```ini
[Globals]
ListenAddress=127.0.0.1
ListenPort=3350
EnableUserWindowManager=true
UserWindowManager=startwm.sh
DefaultWindowManager=startwm.sh
ReconnectScript=reconnectwm.sh

[Security]
AllowRootLogin=false
MaxLoginRetry=4
TerminalServerUsers=tsusers
TerminalServerAdmins=tsadmins
AlwaysGroupCheck=false
RestrictOutboundClipboard=none
RestrictInboundClipboard=none

[Sessions]
X11DisplayOffset=10
MaxSessions=5
KillDisconnected=true
DisconnectedTimeLimit=5
IdleTimeLimit=120
Policy=Default

[Logging]
LogFile=xrdp-sesman.log
LogLevel=INFO
EnableSyslog=true

[LoggingPerLogger]

[Xorg]
param=/usr/lib/xorg/Xorg
param=-config
param=xrdp/xorg.conf
param=-nolisten
param=tcp
param=-logfile
param=.xorgxrdp.%s.log
param=-noreset

[Xvnc]
param=Xvnc
param=-bs
param=-nolisten
param=tcp
param=-localhost
param=-dpi
param=96

[Chansrv]
FileUmask=077
EnableFuseMount=false

[ChansrvLogging]
LogLevel=INFO
EnableSyslog=true

[ChansrvLoggingPerLogger]

[SessionVariables]
PULSE_SCRIPT=/etc/xrdp/pulse/default.pa
```
# 3 guacamole
## 3.1 docker-compose
/home/guacamole/docker-compose.yml


```yaml
services:
  guacamole:
    image: jwetzell/guacamole
    container_name: guacamole
    volumes:
      - ./postgres:/config
      - ./guacamole:/config/guacamole
    ports:
      - 8181:8080
    restart: always
```

## 3.2 配置连接

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412040953446.png)

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412040953112.png)

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412040954562.png)

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412040956906.png)



# 4 用户配置

创建用户

```bash
adduser kiosk4
Adding user `kiosk4' ...
Adding new group `kiosk4' (1003) ...
Adding new user `kiosk4' (1003) with group `kiosk4 (1003)' ...
Creating home directory `/home/kiosk4' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for kiosk4
Enter the new value, or press ENTER for the default
	Full Name []:
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] y
Adding new user `kiosk4' to supplemental / extra groups `users' ...
Adding user `kiosk4' to group `users' ...
```


/home/kiosk1/.xsession
```bash
#!/bin/sh

openbox-session &

export GTK_IM_MODULE=fcitx5
export QT_IM_MODULE=fcitx5
export XMODIFIERS="@im=fcitx5"
export DefaultIMModule=fcitx5
fcitx5 &

#firefox-esr "https://gamma.app/create"
firefox-esr --no-remote --kiosk "https://gamma.app/create"
```

需要先不添加--kiosk参数，安装好firefox需要的插件，然后把`/home/kiosk1/.cache/mozilla/firefox`复制到其他的kioskx用户目录下




# 5 firefox插件

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412041116161.png)


## 5.1 REDIRECTOR
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412041116546.png)

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202412041118511.png)

## 5.2 Stylus

### 5.2.1 code1

URLS on the domain：gamma.app
```
.css-c8wqv { display: none;}
div.chakra-stack.css-1d4nxqf > div:nth-child(1) > div:nth-child(3) > div > button:nth-child(2) {display:none;}
button.chakra-link {display:none;}
div.css-k008qs:nth-child(8) {display:none;}
div.css-k008qs:nth-child(5) {display:none;}
#menu-list-\:r4h\:-menuitem-\:r59\: {display:none;}
#menu-list-\:r4h\:-menuitem-\:r5b\: {display:none;}
.css-1np2ko3 {display:none;}
div.css-1igwmid:nth-child(2) > span:nth-child(4)  {display:none;}
div.chakra-alert:nth-child(2)  {display:none;}
div.css-e7bl9g:nth-child(3) {display:none;}
li.chakra-wrap__listitem:nth-child(1) {display:none;}
li.chakra-wrap__listitem:nth-child(2)  {display:none;}
li.chakra-wrap__listitem:nth-child(4) {display:none;}
#menu-list-\:r4h\:-menuitem-\:rtj\:  {display:none;}
#menu-list-\:r4h\:-menuitem-\:r53\: {display:none;}
.css-ilr7x7 {display:none;}
hr.chakra-divider:nth-child(1) {display:none;}
.css-174i91n {display:none;}
.css-wkblhz {display:none;}
.css-14h18p5 {display:none;}
.css-1cggwyz > div:nth-child(1) {display:none;}
.css-1bk0lf8 {display:none;}
```

### 5.2.2 code2
URLs matching the regexp: https://gamma.app/create

```
button.chakra-button.css-mbvp51 {
    display: none;
}
```


### 5.2.3 code3
URLs starting with: https://gamma.app/create/generate
```
div.chakra-container:nth-child(1) {display:none;}
.css-uv9e93 {display:none;}
```

