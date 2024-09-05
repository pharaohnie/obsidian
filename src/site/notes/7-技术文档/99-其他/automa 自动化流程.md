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