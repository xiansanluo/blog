---
date: 2017-10-19T12:07:49+08:00
title: "性能测试工具locust的使用"
---
# 性能测试工具locust的使用

标签（空格分隔）： 未分类

---

Locust是一个用于可扩展的，分布式的，性能测试的，开源的，用Python编写框架/工具，它非常容易使用，也非常好学。它的主要思想就是模拟一群用户将访问你的网站。每个用户的行为由你编写的python代码定义，同时可以从Web界面中实时观察到用户的行为。
特点：

* 用简单python语言编写测试脚本，非常简单轻便。不需要笨重的UI和臃肿的xml代码，基于协同而非回调。
* 分布式的，可扩展性的，可模拟上百万用户。Locust支持多机器的性能测试，每台机器可以模拟上千用户，当然这可以控制的，
* locust有一个整洁的HTML+JS的用户界面，实时显示相关测试细节。由于用户界面是基于网络的，它是跨平台的和容易扩展。
* 可以测试任何系统，尽管Locust是基于网站的，但它几乎可以测试任何系统，只要你写一个客户端。

我们已经在kubernetes中搭建了一个demo，只需要简单修改就可以直接用来测试你自己的程序。
NOTE:只支持http服务，rpc服务需要编写http的client才能使用
##一、编写测试样例

测试样例请放到https://github.com/xiansan.luo/locustfile 下面，可以参考里面的example进行编写，最后进行提交。

##二、启动k8s loadtest pipline

* 进入spinnaker
* 手动触发pipline（需要修改参数：SCENARIO_FILE为提交的测试样例文件，TARGET_URL为被测服务的endpoint）
* 等待pipline进行到Manual Judgement

##三、打开web页面，进行测试

* 打开staging vpn
* 浏览器输入（这里的ip可以是任意一台staging的机器）
* 进行测试，下载测试结果等

##四、销毁k8s Server Group

* 在k8s的pipline里Manual Judgement点击Continue

更多内容请参考官方文档：https://docs.locust.io/en/latest/what-is-locust.html


