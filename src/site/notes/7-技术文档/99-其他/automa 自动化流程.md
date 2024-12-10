---
{"dg-publish":true,"permalink":"/7-技术文档/99-其他/automa 自动化流程/","tags":["automa"]}
---


在 chrome 中安装 automa

# 1 整体流程
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051027472.png)
读取一组数据 -> 打开页面 -> 用javascript读取变量并给变量赋值 -> 点击某一个元素 -> 在表单中输入内容 -> 点击发送 -> 等待一会儿 -> 获取数据并写入表格 -> 关闭标签 -> 进行循环读取下一个数据。

# 2 循环数据

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051052080.png)

`循环 id` 是引用数据集的名字
要选择 `自定义数据`，然后 `插入数据` -> `导入文件`
需要导入的数据是 `csv` 格式的文件，需要代一个表头。例如:

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051054031.png)

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051054452.png)
导入前要勾选使用表头做为主键。

# 3 新建标签页
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051057364.png)
输入需要打开的链接。

# 4 Javascript 代码
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051056715.png)
插入的代码如下：
```javascript
automaSetVariable('query', automaRefData('loopData', 'data'.query))
```

相当于每次循环会从loopData开始，然后读取 `循环 id` 为 data 的数据中的第一个，然后复制给 query 变量。
左边那个`query` 是复制的变量名。
右侧按个`query` 是 `csv` 数据表的表头。

# 5 点击元素
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051059554.png)

如何获取 css 选择器的值？
先开大要访问的页面。
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051103166.png)

鼠标放到要模拟点击的位置，选择复制图标，复制的内容就是 css 选择器的内容
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051106540.png)

还一种方法，可以写成 `[class="line-clamp-1 overflow-hidden"]`

# 6 表单
表单的功能是模拟键盘输入
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051109448.png)

 css 选择器的值仍然用上一步的方法获取。
 文本域：`{{loopData@data.query}}`就是从 `Javascript 代码` 中赋值的变量。
 `loopData` 表示是循环数据，`data` 是循环数据的 `循环 id`。`query`是 `Juavascript 代码`复制的变量名。

# 7 创建和链接存储表
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051115872.png)

获取文本前需要创建一个存储表，这样才能把获取的文本保存起来。

然后需要把流程和存储表链接起来，如下
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051117131.png)

链接之后如下：
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051117968.png)


# 8 获取文本

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051114768.png)
插入表格，将获取的结果存储表格的`结果`一列。
为了保存完整的信息，把查询内容 `{{loopData@data.query}}` 添加到 `查询` 一列。

万一获取不到文本，需要进行下一个数据的输入和文本的读取，因此。
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051120052.png)

出错时要继续流程，并插入一个 `未找到` 的数据内容，如下：
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051120441.png)

# 9 循环断点

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409051121947.png)

输入循环 `data` 的名字，执行到这里后，返回 `循环数据`，开始下一次文本的输入和获取。


# 10 代码
```json
{
  "extVersion": "1.28.27",
  "name": "专家邮件-智谱",
  "icon": "riGlobalLine",
  "table": [],
  "version": "1.28.27",
  "drawflow": {
    "edges": [
      {
        "data": {},
        "events": {},
        "id": "vueflow__edge-mty8sl0mty8sl0-output-1-waicb54waicb54-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "mty8sl0",
        "sourceHandle": "mty8sl0-output-1",
        "sourceX": 362.42087891230574,
        "sourceY": 234.59448328004402,
        "target": "waicb54",
        "targetHandle": "waicb54-input-1",
        "targetX": 517.5421600622034,
        "targetY": 234.11291548170394,
        "type": "custom",
        "updatable": true
      },
      {
        "data": {},
        "events": {},
        "id": "vueflow__edge-wsnvufkwsnvufk-output-1-exun8syexun8sy-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "wsnvufk",
        "sourceHandle": "wsnvufk-output-1",
        "sourceX": 1255.9218817077779,
        "sourceY": 761.704868754011,
        "target": "exun8sy",
        "targetHandle": "exun8sy-input-1",
        "targetX": 1008.7775857051201,
        "targetY": 486.4435883051458,
        "type": "custom",
        "updatable": true
      },
      {
        "class": "connected-edges",
        "data": {},
        "events": {},
        "id": "vueflow__edge-e_X-ptgupDKmGelxZGbrfe_X-ptgupDKmGelxZGbrf-output-1-soy582ksoy582k-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "e_X-ptgupDKmGelxZGbrf",
        "sourceHandle": "e_X-ptgupDKmGelxZGbrf-output-1",
        "sourceX": -57.65992195906881,
        "sourceY": 224.12865700871384,
        "target": "soy582k",
        "targetHandle": "soy582k-input-1",
        "targetX": -279.8403684138105,
        "targetY": 454.1386644108445,
        "type": "custom",
        "updatable": true
      },
      {
        "data": {},
        "events": {},
        "id": "vueflow__edge-exun8syexun8sy-output-1-5lsy2zi5lsy2zi-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "exun8sy",
        "sourceHandle": "exun8sy-output-1",
        "sourceX": 1240.7775857051201,
        "sourceY": 486.4435883051458,
        "target": "5lsy2zi",
        "targetHandle": "5lsy2zi-input-1",
        "targetX": 1008.783934804678,
        "targetY": 249.7392581644333,
        "type": "custom",
        "updatable": true
      },
      {
        "class": "connected-edges",
        "data": {},
        "events": {},
        "id": "vueflow__edge-soy582ksoy582k-output-1-860j4gj860j4gj-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "soy582k",
        "sourceHandle": "soy582k-output-1",
        "sourceX": -47.840368413810495,
        "sourceY": 454.1386644108445,
        "target": "860j4gj",
        "targetHandle": "860j4gj-input-1",
        "targetX": -296.7166874802559,
        "targetY": 762.7538928735421,
        "type": "custom",
        "updatable": true
      },
      {
        "data": {},
        "events": {},
        "id": "vueflow__edge-860j4gj860j4gj-output-1-ku9d5mhku9d5mh-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "860j4gj",
        "sourceHandle": "860j4gj-output-1",
        "sourceX": -64.71668748025593,
        "sourceY": 762.7538928735421,
        "target": "ku9d5mh",
        "targetHandle": "ku9d5mh-input-1",
        "targetX": 106.40507940750672,
        "targetY": 762.0062053514757,
        "type": "custom",
        "updatable": true
      },
      {
        "data": {},
        "events": {},
        "id": "vueflow__edge-ku9d5mhku9d5mh-output-1-iofl7laiofl7la-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "ku9d5mh",
        "sourceHandle": "ku9d5mh-output-1",
        "sourceX": 338.4050794075067,
        "sourceY": 762.0062053514757,
        "target": "iofl7la",
        "targetHandle": "iofl7la-input-1",
        "targetX": 135.4594602596544,
        "targetY": 463.7114128371334,
        "type": "custom",
        "updatable": true
      },
      {
        "class": "connected-edges",
        "data": {},
        "events": {},
        "id": "vueflow__edge-waicb54waicb54-output-1-nxbpsoenxbpsoe-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "waicb54",
        "sourceHandle": "waicb54-output-1",
        "sourceX": 749.5422210973596,
        "sourceY": 234.11291548170394,
        "target": "nxbpsoe",
        "targetHandle": "nxbpsoe-input-1",
        "targetX": 552.9435223315021,
        "targetY": 503.9544586870801,
        "type": "custom",
        "updatable": true
      },
      {
        "class": "connected-edges",
        "data": {},
        "events": {},
        "id": "vueflow__edge-nxbpsoenxbpsoe-output-1-48nz4df48nz4df-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "nxbpsoe",
        "sourceHandle": "nxbpsoe-output-1",
        "sourceX": 784.9435833666583,
        "sourceY": 503.9544586870801,
        "target": "48nz4df",
        "targetHandle": "48nz4df-input-1",
        "targetX": 512,
        "targetY": 770.59375,
        "type": "custom",
        "updatable": true
      },
      {
        "class": "connected-edges",
        "data": {},
        "events": {},
        "id": "vueflow__edge-48nz4df48nz4df-output-1-wsnvufkwsnvufk-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "48nz4df",
        "sourceHandle": "48nz4df-output-1",
        "sourceX": 744,
        "sourceY": 770.59375,
        "target": "wsnvufk",
        "targetHandle": "wsnvufk-input-1",
        "targetX": 1023.9218817077779,
        "targetY": 761.704868754011,
        "type": "custom",
        "updatable": true
      },
      {
        "data": {},
        "events": {},
        "id": "vueflow__edge-iofl7laiofl7la-output-1-mty8sl0mty8sl0-input-1",
        "markerEnd": "arrowclosed",
        "selectable": true,
        "source": "iofl7la",
        "sourceHandle": "iofl7la-output-1",
        "sourceX": 367.4594602596544,
        "sourceY": 463.7114128371334,
        "target": "mty8sl0",
        "targetHandle": "mty8sl0-input-1",
        "targetX": 130.42087891230574,
        "targetY": 234.59448328004402,
        "type": "custom",
        "updatable": true
      }
    ],
    "nodes": [
      {
        "data": {
          "activeInInput": false,
          "contextMenuName": "",
          "contextTypes": [],
          "date": "",
          "days": [],
          "delay": 5,
          "description": "",
          "disableBlock": false,
          "interval": 60,
          "isUrlRegex": false,
          "observeElement": {
            "baseElOptions": {
              "attributeFilter": [],
              "attributes": false,
              "characterData": false,
              "childList": true,
              "subtree": false
            },
            "baseSelector": "",
            "matchPattern": "",
            "selector": "",
            "targetOptions": {
              "attributeFilter": [],
              "attributes": false,
              "characterData": false,
              "childList": true,
              "subtree": false
            }
          },
          "parameters": [],
          "preferParamsInTab": false,
          "settings": {
            "blockTimeout": 0,
            "debugMode": false
          },
          "shortcut": "",
          "time": "00:00",
          "type": "manual",
          "url": ""
        },
        "events": {},
        "id": "e_X-ptgupDKmGelxZGbrf",
        "label": "trigger",
        "position": {
          "x": -269.6599219590688,
          "y": 188.12865700871384
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "active": true,
          "customUserAgent": false,
          "description": "打开metaso",
          "disableBlock": false,
          "inGroup": false,
          "tabZoom": 1,
          "updatePrevTab": false,
          "url": "https://chatglm.cn/main/gdetail/659e54b1b8006379b4b2abd6?lang=zh",
          "userAgent": "",
          "waitTabLoaded": false
        },
        "events": {},
        "id": "860j4gj",
        "label": "new-tab",
        "position": {
          "x": -276.7166874802559,
          "y": 726.7538928735421
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "description": "",
          "disableBlock": false,
          "findBy": "cssSelector",
          "markEl": false,
          "multiple": false,
          "selector": ".input-box-inner > .scroll-display-none",
          "waitForSelector": true,
          "waitSelectorTimeout": 5000
        },
        "events": {},
        "id": "mty8sl0",
        "label": "event-click",
        "position": {
          "x": 150.42087891230574,
          "y": 198.59448328004402
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "assignVariable": false,
          "clearValue": true,
          "dataColumn": "",
          "delay": "25",
          "description": "",
          "disableBlock": false,
          "events": [],
          "findBy": "cssSelector",
          "getValue": false,
          "markEl": false,
          "multiple": false,
          "optionPosition": "1",
          "saveData": false,
          "selectOptionBy": "value",
          "selected": true,
          "selector": ".input-box-inner > .scroll-display-none",
          "type": "text-field",
          "value": "{{loopData@data.query}}的邮箱。只输出单行、姓名、邮箱，格式为“单位：xxx，姓名：xxx，邮箱：xxx”。如果查不到，输出“无”。",
          "variableName": "",
          "waitForSelector": true,
          "waitSelectorTimeout": 5000
        },
        "events": {},
        "id": "waicb54",
        "label": "forms",
        "position": {
          "x": 537.5421600622034,
          "y": 198.11291548170394
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "addExtraRow": true,
          "assignVariable": false,
          "dataColumn": "BQT1a",
          "description": "找到",
          "disableBlock": false,
          "extraRowDataColumn": "1SdjG",
          "extraRowValue": "{{loopData@data.query}}",
          "findBy": "cssSelector",
          "includeTags": false,
          "markEl": false,
          "multiple": false,
          "onError": {
            "dataToInsert": [
              {
                "name": "EaH5U",
                "type": "table",
                "value": "{{loopData@data.query}}"
              },
              {
                "name": "mgwTG",
                "type": "table",
                "value": "未找到"
              }
            ],
            "enable": true,
            "insertData": true,
            "retry": false,
            "retryInterval": 2,
            "retryTimes": 1,
            "toDo": "continue"
          },
          "prefixText": "",
          "regex": "",
          "regexExp": [],
          "saveData": true,
          "selector": "[class=\"markdown-body md-body tl\"]",
          "settings": {
            "blockTimeout": 0,
            "debugMode": false
          },
          "suffixText": "",
          "useTextContent": false,
          "variableName": "",
          "waitForSelector": true,
          "waitSelectorTimeout": 5000
        },
        "events": {},
        "id": "wsnvufk",
        "label": "get-text",
        "position": {
          "x": 1043.9218817077779,
          "y": 725.704868754011
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "activeTab": true,
          "allWindows": false,
          "closeType": "tab",
          "description": "",
          "disableBlock": false,
          "url": ""
        },
        "events": {},
        "id": "exun8sy",
        "label": "close-tab",
        "position": {
          "x": 1028.7775857051201,
          "y": 450.4436188227239
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "description": "",
          "disableBlock": false,
          "elementSelector": "",
          "fromNumber": 1,
          "loopData": "[\n  {\n    \"query\": \"南通大学 于洪爽\"\n  }\n  }\n]",
          "loopId": "data",
          "loopThrough": "custom-data",
          "maxLoop": "250",
          "referenceKey": "",
          "resumeLastWorkflow": false,
          "reverseLoop": false,
          "startIndex": 0,
          "toNumber": 10,
          "variableName": "",
          "waitForSelector": false,
          "waitSelectorTimeout": 5000
        },
        "events": {},
        "id": "soy582k",
        "label": "loop-data",
        "position": {
          "x": -259.8403684138105,
          "y": 418.1386644108445
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "code": "automaSetVariable('query', automaRefData('loopData', 'data'.query))",
          "context": "website",
          "description": "",
          "disableBlock": false,
          "everyNewTab": false,
          "preloadScripts": [],
          "runBeforeLoad": false,
          "timeout": 20000
        },
        "events": {},
        "id": "iofl7la",
        "label": "javascript-code",
        "position": {
          "x": 155.4594602596544,
          "y": 427.7114128371334
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "clearLoop": false,
          "disableBlock": false,
          "loopId": "data"
        },
        "events": {},
        "id": "5lsy2zi",
        "label": "loop-breakpoint",
        "position": {
          "x": 1028.783934804678,
          "y": 174.1455081644333
        },
        "type": "BlockLoopBreakpoint"
      },
      {
        "data": {
          "description": "",
          "disableBlock": false,
          "flowBlockId": "",
          "specificFlow": false,
          "timeout": 10000
        },
        "events": {},
        "id": "ku9d5mh",
        "label": "wait-connections",
        "position": {
          "x": 126.40507940750672,
          "y": 726.0062053514757
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "description": "",
          "disableBlock": false,
          "findBy": "cssSelector",
          "markEl": false,
          "multiple": false,
          "selector": "img.enter_icon",
          "waitForSelector": true,
          "waitSelectorTimeout": 5000
        },
        "events": {},
        "id": "nxbpsoe",
        "label": "event-click",
        "position": {
          "x": 572.9435223315021,
          "y": 467.9544892046582
        },
        "type": "BlockBasic"
      },
      {
        "data": {
          "disableBlock": false,
          "time": "35000"
        },
        "events": {},
        "id": "48nz4df",
        "label": "delay",
        "position": {
          "x": 532,
          "y": 712
        },
        "type": "BlockDelay"
      }
    ],
    "position": [
      496,
      79
    ],
    "viewport": {
      "x": 496,
      "y": 79,
      "zoom": 1
    },
    "zoom": 1
  },
  "settings": {
    "blockDelay": 0,
    "debugMode": false,
    "defaultColumnName": "column",
    "execContext": "popup",
    "executedBlockOnWeb": false,
    "inputAutocomplete": true,
    "insertDefaultColumn": false,
    "notification": true,
    "onError": "stop-workflow",
    "publicId": "",
    "restartTimes": 3,
    "reuseLastState": false,
    "saveLog": true,
    "tabLoadTimeout": 30000
  },
  "globalData": "{\n\t\"key\": \"value\"\n}",
  "description": "",
  "includedWorkflows": {}
}
```