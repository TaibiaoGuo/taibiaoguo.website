+++
author = "Taibiao Guo"
title = "生产级别的 BAAS 方案设计和实现"
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

虽然众多云服务厂商已经对外提供了SaaS级别的HyperLedger Fabric服务，但要在私有云环境中构建自己的生产级别HyperLedger Fabric服务仍然存在诸多挑战。

[Hyperledger Fabric 2.2 LTS](https://www.hyperledger.org/blog/2020/07/20/new-release-hyperledger-fabric-2-2-lts) (后续简称 Fabric) 是超级账本的最新长期支持版本，在其文档中提供了测试网络（test-network）替代了之前的"构建您的第一个网络"(BYFN) [1] 。

本文主要使用的工具为 Hyperledger Fabric 2.2 LTS 、 Kubernetes 、 Helm 。

## 预备知识

### Fabric 相关概念

### Kubernetes 相关概念

* [Kubernetes Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) - Pod 表示 Kubernetes 集群中可部署的最小单元，用于对必须被视为单个单元的容器进行分组。
* [Kubernetes 作业](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/) - 一个作业可创建一个或多个 Pod，并确保成功终止指定数量的 Pod。Pod 成功完成之后，该作业会跟踪成功完成情况。
* [Kubernetes 部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) - 部署是一种 Kubernetes 资源，您可以在此指定运行应用所需的容器和其他 Kubernetes 资源，比如持久存储、服务或注释。
* [Kubernetes 服务](https://kubernetes.io/docs/concepts/services-networking/service/) - Kubernetes 服务集合了一系列的 Pod，并为集群中面向其他服务的 Pod 提供网络连接，无需公开每个 Pod 的真实私有 IP 地址。
* [Kubernetes 持久卷 (PV)](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) - 持久卷是用户*请求获得*持久存储（比如 NFS 文件存储）的一种方式。


### Hlem 相关概念


参考资料
- [1] [在IBM Cloud上部署Hyperledger Fabric网络](https://github.com/IBM/blockchain-network-on-kubernetes)

- [2] Wikipedia