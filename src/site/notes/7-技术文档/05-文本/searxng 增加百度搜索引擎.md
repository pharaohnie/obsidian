---
{"dg-publish":true,"permalink":"/7-技术文档/05-文本/searxng 增加百度搜索引擎/","tags":["searxng","百度"]}
---

在 searxng 的 `settings.yml` 中增加
```yaml
engines:
  - name : baidu
    engine : xpath
    shortcut : bd
    enable_http: true
    search_url : "https://www.baidu.com/s?wd={query}"
    url_xpath : '//h3[@class="c-title t t tts-title"]/a/@href'
    title_xpath : '//h3[@class="c-title t t tts-title"]/a'
    content_xpath : '//span[@class="content-right_2s-H4"]'
    cookies:
      BAIDUID: "164FFF8F3E3555824F264E50867EFE22:FG=1"
      BAIDUID_BFESS: "164FFF8F3E3555824F264E50867EFE22:FG=1"
    weight: 3
    categories : general
    disabled: false
```

在测试的时候，先把其他的搜索引擎的`disabled`设置为`true`，以免有干扰结果。
注意：url要加上`@href`来获取


下一步，重点是如何找到`url_xpath`、`title_xpath`、`content_xpath`。
先随便在百度搜一下东西。点击其中一个条目的标题，`右键` -> `检查`。
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409041117447.png)


可以往上挪一点扩大选取范围
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409041118010.png)

`右键` -> `复制` -> `复制 outerHTML`
![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409041119435.png)

出来的结果是这样的
```html
<div class="c-container" data-click=""><div has-tts="false"><!--659--><h3 class="c-title t t tts-title"><!--664--><!--665--><a class="
                " href="https://www.baidu.com/link?url=-0jArR3iNadeVDv3SJILRvEArPno2DkIGqwP3lPGsXbnoynK-T_Eak0hcvOcN3XB&amp;wd=&amp;eqid=86917e710000d4900000000266d7d11c" data-showurl-highlight="true" target="_blank" tabindex="0" data-click="{&quot;F&quot;:&quot;778317EA&quot;,&quot;F1&quot;:&quot;9D73F1C4&quot;,&quot;F2&quot;:&quot;4CA6DE6A&quot;,&quot;F3&quot;:&quot;54E5243F&quot;,&quot;T&quot;:1725419805,&quot;y&quot;:&quot;FDF4FF5D&quot;}" aria-label=""><em>李国</em>良-<em>中国科学院</em>大学-UCAS</a><!--666--><!--667--></h3><!--663--><!--660--><div style="margin-bottom: -4px;"><!--669--><!--670--><!--671--><!--672--><!--673--><!--674--></div><!--675--><div><!--678--><div class="c-gap-top-small"><!--680--><!--681--><!--682--><!--683--><!--684--><!--685--><span class="content-right_1THTn">李国良 男 硕导 <em>中国科学院</em>生态环境研究中心 <em>电子邮件</em>: glli@rcees.ac.cn 通信地址: 北京市海淀区双清路18号 邮政编码: 100085 教育背景 2007-09--2011-06 中国科学院生态环...</span><!--686--><!--687--><!--688--><div class="c-row source_1Vdff OP_LOG_LINK c-gap-top-xsmall source_s_25cVc "><a href="http://www.baidu.com/link?url=-0jArR3iNadeVDv3SJILRvEArPno2DkIGqwP3lPGsXbnoynK-T_Eak0hcvOcN3XB" target="_blank" class="siteLink_9TPP3" aria-hidden="false" tabindex="0" aria-label=""><!--692--><span class="c-color-gray" aria-hidden="true">中国科学院大学</span><!--693--></a><!--691--><div class="c-tools tools_47szj" id="tools_479082148230189136_2" data-tools="{'title': &quot;李国良-中国科学院大学-UCAS&quot;,
            'url': &quot;http://www.baidu.com/link?url=-0jArR3iNadeVDv3SJILRvEArPno2DkIGqwP3lPGsXbnoynK-T_Eak0hcvOcN3XB&quot;}" aria-hidden="true"><i class="c-icon icon_X09BS"></i></div><!--694--><!--695--><!--696--><!--697--><!--698--><!--699--><!--700--><!--701--><!--700--></div><!--689--><!--702--></div><!--678--><!--677--><div></div></div></div><!--703--><div><!--706--><!--707--></div><!--704--></div>
```

把内容扔给 gpt，提示词如下

```text
'''
  - name : baidu
    engine : xpath
    shortcut : bd
    enable_http: true
    search_url : "https://www.baidu.com/s?wd={query}"
    url_xpath : '//*[@class="c-title"]/h3/a/@href'
    title_xpath : '//*[@class="c-title"]/h3/a'
    content_xpath : '//*[@class="c-title"]/h3/a'
    cookies:
      BAIDUID: "164FFF8F3E3555824F264E50867EFE22:FG=1"
      BAIDUID_BFESS: "164FFF8F3E3555824F264E50867EFE22:FG=1"
    weight: 3
    categories : general
    disabled: false
'''
这是 searxng 中的 baidu 引擎的配置，需要使用 xpath 进行匹配。但现在的配置搜索不到结果
我会给你title 部分的网页的源代码，帮我提取title_xpath
'''
这里填写outerHTML的内容
'''
```

gpt 的反馈还是很准的，如下

![image.png](https://nxl-tuchuang.oss-cn-beijing.aliyuncs.com/202409041122682.png)
