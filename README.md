## 背景介绍

在当前阶段，利用性能测试工具LoadRunner对B/S架构类应用系统进行脚本录制时经常会出现因异常报错而无法完成脚本录制工作的情况，该问题一直困扰着广大性能测试工作者，且始终没有一个彻底的解决方案。但利用抓包工具对通讯数据进行抓包时，却能正确获取得到通讯数据。

针对这种情况，我提出了一套新的解决方案，即开发了一款脚本转换工具，将由Fiddler抓包工具抓包获得的saz数据包转换为LoadRunner测试脚本，供测试人员在LoadRunner中直接进行测试使用。通过采用此方案，可成功解决B/S架构类系统脚本录制异常的问题。


## 开发思路

从LoadRunner脚本录制功能的原理出发，其无非也是记录通讯交互过程中的所有请求，然后转换为相应函数，最终形成测试脚本。
在利用网络抓包工具Fiddler2对通讯数据进行抓包时，所有浏览器端请求和服务器端响应均成功地被记录下来，抓包过程中IE浏览器也没有出现异常退出的情况。
因此，我们完全可以利用抓包工具Fiddler2抓取得到的数据包，将其中的每一个请求人工转换为LoadRunner所能识别的函数，从而绕过LoadRunner录制功能方面的缺陷，形成LoadRunner脚本。

% 对LoadRunner脚本录制缺陷方面的改进
% 李隆
% April 29, 2014

# LoadRunner测试流程简介

## LoadRunner原理图


![](images/loadrunner_ylt.png)

## LoadRunner测试流程


![](images/lr_test_step.png)

# 问题及现象

## 脚本录制时IE浏览器异常


![](images/IE_error.png)

## 问题影响分析


![](images/lr_test_step_error.png)

# 分析思路

## LoadRunner脚本录制原理


![](images/vugen_ylt.jpg)

## LoadRunner录制脚本分析

- web_url
- web_submit_form
- web_submit_data
- web_image
- web_custom_request

## Fiddler2 抓包工具的引入


![](images/fiddler_ylt.png)

## Fiddler2抓包数据图示
![Fiddler2抓包数据](images/spaghetti.jpg)

## 手工编写脚本


![](images/lr_script.png)

## 初步思路

1、获取被测性能点的所有业务请求数据。
2、读取每一条HTTP请求，对其请求内容进行分析。
3、将所有HTTP请求逐个转换为LoadRunner函数。

## 存在的问题

- 手工转换工作繁杂，耗费大量时间和精力
- 手工转换工作依赖测试人员个人能力，不易推广

# 解决方案

## saz文档格式分析

![](images/saz_zip.png)


![](images/saz_zip_1.png)


![](images/saz_zip_2.png)


![](images/saz_zip_client.png)

## 程序实现思路

- 加载.saz文件，将其转换为.zip文件；
- 加载.zip文件，获取其中raw文件夹下所有sessid#_c.txt文件，并读取其内容，存储为clientRequestList；
- 对于clientRequestList中的每一条请求，参照web_custom_request的函数说明，依次转换成为web_custom_request函数，最终形成LoadRunner测试脚本。

