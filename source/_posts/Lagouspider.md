title: "程序员拉钩找工作的另类姿势"
date: 2016-03-24 08:56:34
description: "先放Github地址：https://github.com/LiZhenNet/GetJobsFromLagou"
categories: NodeJS
tags: 
- NodeJS
- 爬虫
- MongoDB
---

**先放Github 地址：[https://github.com/LiZhenNet/GetJobsFromLagou](https://github.com/LiZhenNet/GetJobsFromLagou)**
### 主要功能
 1. 使用方式GitHub README上有介绍
 2. 主要是爬取拉钩职位列表，统计数据根据工作年限给予在薪资方面的参考
 3. 分享了一些关于简历方面的文章
 4. 根据薪资倒序排列职位列表，这个有用 哈哈哈。
 5. 统计一下各个工作年限的职位数
>最近开始学习Nodejs，学习嘛，要学以致用，看到cheerio 这个库，就打算写个爬虫，试一下。于是瞄准了拉钩。
### 学习过程如下：
 1. 首先我先模拟请求了拉钩NodeJs的职位列表。 发现返回的HTML 并没有职位信息，于是没用使用到cheerio来提取数据。
![](http://images2015.cnblogs.com/blog/609388/201603/609388-20160323202016698-547581648.png)

 2. chrome 打开了职位列表，在NetWork 中发现职位信息是AJAX 请求接口返回的，可以看到请求参数
![](http://images2015.cnblogs.com/blog/609388/201603/609388-20160323202134261-1647982115.png)

 在浏览器使用Get请求该接口发现可以正常返回数据。
![](http://images2015.cnblogs.com/blog/609388/201603/609388-20160323202215198-1237173888.png)

 3. 于是使用superagent请求Ajax请求的接口地址，发现可以返回数据，并没有接口请求次数的限制。

> 发现了坑，该接口不管pn 传多少 返回的结果中pageNo和currentPageNo都为1

 4. 获取到数据后，需要存储，考虑过本地文件或者数据库，最后选择了数据库，在Mysql和MongoDB中选择，最后选择了不怎么熟悉MongoDB，顺便练习使用了比较常见的Orm--Mongooes

 5. 获得到数据后，需要展现数据，于是用Express 写了Web项目来展示数据。 用到了Echarts 来做可视化。
![](http://images2015.cnblogs.com/blog/609388/201603/609388-20160323202421339-12231915.png)

> 主要分析了，根据工作年限统计职位数，根据工作年限算出薪资的最小、最大、平均值，  按薪资排序职位，和我之前收藏的一些简历方面的知识。

![image](https://github.com/LiZhenNet/GetJobsFromLagou/raw/master/Lagou/public/images/function.jpg)

### 学习到了什么： 
Nodejs 基础，mongoose使用，Express使用，Echarts使用等等。

### 还有什么不足：
改进异步调用，学习promise等