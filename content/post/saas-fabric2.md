+++
author = "Taibiao Guo"
title = "生产级 BAAS 方案设计和实现"
date = "2021-05-13"
description = "HyperLeger Fabric 2.2 LTS"
tags = [
    "HyperLedger Fabric",
    "Blockchain",
    "Kubernetes",
    "Helm",
]
categories = [
    "架构师视角",
    "区块链之路",
]
+++

目前大部虽然众多云服务厂商已经对外提供了SaaS级别的HyperLedger Fabric服务，但要在私有云环境中构建自己的生产级别HyperLedger Fabric服务依然是非常复杂的事情。
分教程都源自于 HyperLedger Fabric 文档中提供的测试网络（test-network）或已经在 `v2.2`中删除了的"构建您的第一个网络"(BYFN) 或者 
[minifabric](https://github.com/hyperledger-labs/minifabric) 这类快速 HyperLedger Fabric 环境搭建工具。
进行的超级账本部署, 或是 HyperLedger Fabric 1.4 TLS 的Kubernetes部署方案 [2] 。

## 预备知识

[Hyperledger Fabric 2.2 LTS](https://www.hyperledger.org/blog/2020/07/20/new-release-hyperledger-fabric-2-2-lts) (后续简称 Fabric) 是超级账本的最新长期支持版本

本文主要使用的工具为 Hyperledger Fabric 2.2 LTS 、 Kubernetes 、 Helm3 。

### Fabric 相关概念

* [Fabric 身份](https://hyperledger-fabric.readthedocs.io/zh_CN/release-2.2/identity/identity.html) - 
* [Fabric 成员服务提供者](https://hyperledger-fabric.readthedocs.io/zh_CN/release-2.2/membership/membership.html) - 
* [Fabric 证书颁发机构](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html) - 
* [Fabric 账本](https://hyperledger-fabric.readthedocs.io/zh_CN/release-2.2/membership/membership.html) - 
* [Fabric 链码](https://hyperledger-fabric.readthedocs.io/zh_CN/release-2.2/smartcontract/smartcontract.html) - 
* [Fabric 节点](https://hyperledger-fabric.readthedocs.io/zh_CN/release-2.2/peers/peers.html) - 

### Kubernetes 相关概念

* [Kubernetes Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) - Pod 表示 Kubernetes 集群中可部署的最小单元，用于对必须被视为单个单元的容器进行分组。
* [Kubernetes 作业](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) - 一个作业可创建一个或多个 Pod，并确保成功终止指定数量的 Pod。Pod 成功完成之后，该作业会跟踪成功完成情况。
* [Kubernetes 部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) - 部署是一种 Kubernetes 资源，您可以在此指定运行应用所需的容器和其他 Kubernetes 资源，比如持久存储、服务或注释。
* [Kubernetes 服务](https://kubernetes.io/docs/concepts/services-networking/service/) - Kubernetes 服务集合了一系列的 Pod，并为集群中面向其他服务的 Pod 提供网络连接，无需公开每个 Pod 的真实私有 IP 地址。
* [Kubernetes 持久卷 (PV)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) - 持久卷是用户*请求获得*持久存储（比如 NFS 文件存储）的一种方式。

### Hlem 相关概念

##  SaaS 设计



## SaaS 实现

### 环境准备

> 云原生(Cloud Native)相关技术版本迭代很快，API会随着版本的演进快速变化，本文章没有对环境进行广泛测试，若您没有相关经验，请务必选择相同的软件版本进行实验。

* 本地搭建：
    * [docker](https://www.docker.com/) v18.03 或更新。
    * [kubernetes]() v1.16 或更新。
    * [Helm]() v3 或更新。
    
* 云环境：

## 未来工作

限于字数，本文主要围绕对生产级 BaaS 服务的云原生改造进行了阐述，相比于 HyperLedger Fabric 的测试环境，本文所定义的生产级 HyperLedger Fabric
环境的可用性和安全性得到了提高，能够实现按需扩容并且服务非常容易迁徙，这些特性主要还是要感谢云原生技术的快速发展。

目前市场上大部分云服务提供商都开放了 BaaS 服务，相比于本文的设计方案，可以提供更多实用的功能。具体来说，主要包括：

* 多租户隔离：

* 遥测监控：

* 日志收集：

* 不停机升级：

* 跨链：

## 参考资料

- [1] [minifabric](https://github.com/hyperledger-labs/minifabric)

- [2] [在IBM Cloud上部署Hyperledger Fabric网络](https://github.com/IBM/blockchain-network-on-kubernetes)

