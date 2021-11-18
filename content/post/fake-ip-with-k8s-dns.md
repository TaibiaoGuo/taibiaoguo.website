+++
author = "Taibiao Guo"
title = "透明代理网关引发k8s pod域名解析失败的排查思路记录"
date = "2021-11-18"
description = "k8s的计算、存储、网络、调度设计中，网络尤为复杂。k8s是一个精英阶层的产物，k8s脱胎于google的内部系统Borg, 其定位又是一个“Platform for Platform”平台。k8s的用户诉求和k8s项目定位存在明显错位，k8s的经历和我相似，沉醉于k8s快速部署复杂系统的能力和生态及底层基础设施屏蔽能力，又深陷k8s的复杂度泥淖无法自拔，可谓是才出龙潭，又入虎穴。"
tags = [
    "Kubernetes",
    "DNS",
]
categories = [
    "研发日记",
]
+++

## 前言

为了方便k8s应用的开发与调试，我的k8s开发环境会配置透明代理网关，无需再为每一个软件设置代理。在日常的镜像编译、依赖拉取等过程中一直顺风顺水，直到遇到了因透明代理与k8s网络冲突导致pod无法解析dns的故障，网上相关资料极少，特此记录给志同道合之人。

k8s的计算、存储、网络、调度设计中，网络尤为复杂。k8s是一个精英阶层的产物，k8s脱胎于google的内部系统Borg[1],其定位又是一个“Platform for Platform”平台[2]。k8s的用户诉求和k8s项目定位存在明显错位，k8s的经历和我相似，沉醉于k8s快速部署复杂系统的能力和生态及底层基础设施屏蔽能力，又深陷k8s的复杂度泥淖无法自拔，可谓是才出龙潭，又入虎穴。毕竟k8s本身一旦出现故障，花费的排错成本和人员素质要求会非常高。

我只能步步深入k8s dns机制和透明代理的协议细节，定位症结，最终解决了这个问题。话不多说，下面阐述我的k8s网络问题排错思路。

## 起因
在Pod内部无法使用域名访问外部网络（例如访问百度）,而k8s所在宿主机使用域名访问外部网络时则可以正常返回内容。
```
$ curl www.baidu.com
upstream connect error or disconnect/reset before headers. reset reason: local reset(base) 
```


## 错误初排

```
kubectl get pod -n kube-system
NAME                                       READY   STATUS      RESTARTS   AGE
coredns-8496bbfb78-p5jgh                   1/1     Running     0          24d
coredns-8496bbfb78-rw6h2                   1/1     Running     0          13d
default-http-backend-6946487d9b-b4mlx      1/1     Running     0          13d
etcd-k8s-master-node1                      1/1     Running     3          24d
etcd-snapshot-1635085686-89ws9             0/1     Completed   0          24d
etcd-snapshot-1637143200-2qwr2             0/1     Completed   0          15h
etcd-snapshot-1637164800-f45zg             0/1     Completed   0          9h
etcd-snapshot-1637186400-8v6cs             0/1     Completed   0          3h53m
kube-apiserver-k8s-master-node1            1/1     Running     4          24d
kube-controller-manager-k8s-master-node1   1/1     Running     5          24d
kube-flannel-ds-5dh72                      1/1     Running     0          2d
kube-flannel-ds-65bfv                      1/1     Running     0          2d
kube-flannel-ds-q7pgs                      1/1     Running     0          2d
kube-flannel-ds-tdhtq                      1/1     Running     0          2d
kube-proxy-48vbx                           1/1     Running     0          24d
kube-proxy-g8n5w                           1/1     Running     1          24d
kube-proxy-kh5vb                           1/1     Running     0          24d
kube-proxy-mfjd7                           1/1     Running     1          24d
kube-scheduler-k8s-master-node1            1/1     Running     5          24d
metrics-server-bfb48b9f9-ldg2t             1/1     Running     0          13d
snapshot-controller-0                      1/1     Running     0          12d
```

## 查找资料

### Fake-ip

#### 直连
DNS服务是一个多级缓存网络，若DNS请求未命中，DNS请求会经浏览器、操作系统、各级DNS服务器层层转发直到获取解析结果。

在不使用代理的情况下，解析一个不在DNS缓存中的域名，其流程为：

1. 调用操作系统 `getaddrinfo` 方法请求域名解析结果
2. 操作系统读取自身DNS缓存，但因为操作系统也没有DNS缓存记录，因此请求会扩散到DNS服务器
3. 操作系统使用UDP协议向DNS服务器发起解析请求
4. DNS服务器会层层上溯直到找到解析结果，向操作系统返回解析结果
5. 操作系统获得域名对应IP地址

![直连](//www.plantuml.com/plantuml/png/ZP4_JuD06CPtFiNP7KZN3gO3ioRj9xZ2MOZbZjozfEfaTB6BshH64qa7E_WdDjP4KnEhVXavsFeLF11DMwEHXV3-dlizl50LW3c4VajRyQZT2Xs2N810L4oJuxRHzC0D6w4Wt6OK20N3PhxP4BRRndp73DH17LpY4s7wM03XPl4aDWdOxhREGcXvbCmbzILaP4ZJi0sC8tLbPGYB_Au5c8DdUm6cWD1wFPipiJYrrkhbREvGKVv13CCecBN8IWgY6cDGAdXDq_9cVLKrL51yr4qV9ogKNWxbu3YzcyZxFOq9nRTK05LGEjvF-bUeIG0x3lVe1aDok9iyNCZeDex6oRGNZyvcqmCPtSJTb_UdqMpQ-HJahweCidCjJuRB7JbKBKNg7zBYHBxbNrhhIslzffrtvl5YrRT-V3xuDYRFFJc8YbtoZwOYXvFuzNGvKYNKKN_t0m00)

#### 代理
在使用透明代理网关的情况下，透明代理网关会将使用代理的请求中转到代理客户端处理，主要包括DNS转发和代理转发两个步骤。

|方案名称|思路|
| --- | --- |
|DNS转发| |
|代理转发| |

#### 透明代理

### CoreDNS

## 再次排错

## 解决方案

## 总结

## 参考资料

- [1] [Borg: Kubernetes 的前身](https://kubernetes.io/zh/blog/2015/04/borg-predecessor-to-kubernetes/)

- [2] [Kubernetes 这么香，为什么谷歌还坚持使用 Borg？](https://www.infoq.cn/article/jUhMWqTYAaD6z3okKjNj)

- [2] [浅谈在代理环境中的 DNS 解析行为](https://blog.skk.moe/post/what-happend-to-dns-in-proxy/)

- [2] [CoreDNS Manual](https://coredns.io/manual/toc/#configuration)

- [2] [RFC3089 - A SOCKS-based IPv6/IPv4 Gateway Mechanism](https://www.rfc-editor.org/rfc/rfc3089)