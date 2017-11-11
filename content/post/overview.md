
---
date: 2017-10-19T12:07:49+08:00
title: "服务开发"
---

# overview（服务开发）

标签（空格分隔）： 服务 流程 介绍

[TOC]

在一个服务的生命周期中（包括开发，测试，构建，部署，监控，报警等），开发人员可能会用到的一些wiki和基础的组件。本文简单介绍了这些wiki和组件，罗列了他们的入口，目的是协助开发人员快速找到需要的工具和wiki。

##一、准备工作

###1.1 [工程师wiki](https://phab.com/w/engineer/)

 这是必看的wiki。里面涉及新人账号的开通，git指南，项目管理，CI/CD等。是基础必备的工作。

###1.2 [Platform Team](https://phab.com/w/engineer/platform/)

- 里面涉及了vpn账号的申请（由于我们使用的是aws的vpc网络，目前访问spinnaker、playdock、grafana等都需要通过vpn）
- iam是公司内部一个权限系统，通过iam账号，可以登录playdock
- 工具黄页，里面包含了几乎所有可能涉及到的基础工具平台。

##二、开发阶段

###2.1 资源申请
在开发阶段，可能会用到许多资源，比如mysql，redis，mq等。相关的wiki如下：

 - kafka，zookeeper 可以在[工具黄页](https://phab.com/w/engineer/platform/portal/)基础架构服务中找到相应的wiki
 - 这里已经有一些[公用资源](https://phab.com/w/engineer/ops/aws/apply/)，比如staging的rds，redis等。
<span id="playdock"></span>
 - 如果需要额外申请比如S3或其他AWS的资源时。基本步骤参考[staging repository](https://git.com/ops/rasen)或者[prod repository](https://git.com/ops/spiral)的README。同时还可能需要阅读以下wiki：（注意：playdock使用内部[iam](https://phab.com/w/engineer/iam/)账号登陆）

 > [如何写 Terraform 配置代码](https://phab.com/w/engineer/ops/playdock/terraform/) 上面提到的两个repository都是使用Terraform格式组织的
> [Playdock101](https://phab.com/w/engineer/ops/playdock/101/)
> [Playdock](https://phab.com/w/engineer/ops/playdock/)

###2.2 登陆staging机器

我们建议尽量不要登陆机器进行操作，但是如有必要，也是可以申请的。可以参考[ssh staging](https://phab.com/w/engineer/ops/aws-dev-ssh/)

##三、CI

###3.1 代码构建、测试、生成镜像

目前使用了gitlab ci，参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中基础设施`gitlab ci`。那里面还包含了最佳实践，建议在自己编写`gitlab-ci.yml`多进行参考。


###3.2 本地调试生成的镜像

本地想调试时，可能会用到构建出来的镜像。这个时候可以通过[工具黄页](https://phab.com/w/engineer/platform/portal/)中的`Private Repository`的harbor来获取镜像。

##四、CD

对于部署，我们使用了[kubernetes](https://phab.com/w/engineer/ops/kubernetes/)集群，通过spinnaker进行部署。
###4.1 使用CI推送的镜像作为触发器，进行自动构建

参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中基础设施`spinnaker`

###4.2 创建`LoadBalance`并挂载到相应服务下

针对不同目的的服务，有以下三种lb：

- 供k8s集群内部使用
- 需要集群外，内网之间访问
- 需要外网访问

配置方法请参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中基础设施`spinnaker`

###<span id="jump">4.3 暴露`/metrics`，供`prometheus`接入</span>

需要在配置`spinnaker`的`pipline`时，额外增加几个pod annotations。具体参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中基础设施`spinnaker`

###4.4 访问其他服务的地址

在[spinnaker](https://spinnaker.com/,针对外网的lb)相应服务的`LOAD BANLANCERS`下，可以看到相应的服务地址。如果是需要访问外网地址，请查看相应的域名。对于k8s内外之间的访问，可以参考[Kubernetes (k8s)](https://phab.com/w/engineer/ops/kubernetes/)的FAQ部分。

###4.5 服务的动态扩容缩容，资源配额，健康检查等

参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中基础设施`spinnaker`

##五、日志

###5.1 使用kibana搜集日志
集群里的服务，只要日志打到stdout，就可以使用kibana进行日志的收集。如果不是集群内服务，请参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中日志收集`fluentd`进行配置。

###5.2 日志查询
参考[工具黄页](https://phab.com/w/engineer/platform/portal/)中日志收集`kibana`。里面包含了staging和prod的kibana地址。登录账号可以找@liang开通。
这里还有[Kibana常用查询](https://phab.com/w/engineer/ops/kibana/)和[Kibana日志监控](https://phab.com/w/engineer/ops/kibana/kibana日志监控/)

###5.2 使用sentry收集错误日志并告警

针对错误日志，还可以通过sentry直接进行收集，sentry的地址也可以通过[工具黄页](https://phab.com/w/engineer/platform/portal/)得到。sentry 的用法可以参考[github sentry](https://github.com/getsentry/sentry)。

##六、监控报警

以下内容都可以通过[工具黄页](https://phab.com/w/engineer/platform/portal/)中找到他们的地址和wiki。

###6.1 接入prometheus并添加报警

prometheus会收集服务暴露的metrics（当然不止服务，还有k8s集群本身，aws的ec2等），并通过pagerduty进行监控报警。暴露`/metrics`的方法请参考[4.3](#jump)
[Prometheus-Metric&Alert](https://phab.com/w/engineer/ops/prometheus/)里介绍了：

- 各个环境的prometheus地址
- 服务的接入方法，包括ec2和kubernetes
- 报警的添加和查看

###6.2 通过grafana查看监控图表

grafana展示了许多的监控信息，里面有许多的dashboard可以使用，比如:

- `shared-pods` 展示了k8s集群的pod的状态
- `shared-rds` 展示了数据库的监控信息
- `shared-nodes` 展示了ec2的一些监控信息

当然，你也可以自定义自己服务的dashboard，一些起名规范请参考[Grafana 起名规范](https://phab.com/w/engineer/ops/grafana/)


###6.2 通过pagerduty申请账号，报警通知

在`prometheus`中添加了报警，当报警触发的时候，会通知`pagerduty`进行报警。[工具黄页](https://phab.com/w/engineer/platform/portal/)中可以看到具体操作方法。开通账号也是需要通过`playdock`，`playdock`具体使用方法可以参考[资源申请](#playdock)

###6.3 ruby 通过newrelic监控服务性能

其地址在[工具黄页](https://phab.com/w/engineer/platform/portal/)中可以找到。

###6.4 调用链跟踪

对于微服务的架构，由于服务数量很多，可能一次请求会形成内部系统的多次调用。通过`zipkin`，我们可以追踪到一次调用所经过的路径和每个路径的延迟等，帮助我们优化服务，分析业务等。
zipkin是通过kafka接入的，详细方法请参考：

- [zipkin 地址和接入方式](https://phab.com/w/engineer/ops/zipkin/)
- [kafka](https://phab.com/w/engineer/kafka/)
- [zipkin-go-opentracing](https://github.com/openzipkin/zipkin-go-opentracing)
- [zipkin-ruby](https://github.com/openzipkin/zipkin-ruby)
