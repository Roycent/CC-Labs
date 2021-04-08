# 云计算实验指南

[toc]

---

## 如何进行实验

### 访问云平台

可以通过[北航软件学院云平台-VPN入口1](https://cloud-beihangsoft-cn.e1.buaa.edu.cn/)或者[北航软件学院云平台-VPN入口2](https://cloud-beihangsoft-cn.e2.buaa.edu.cn/)（`https://cloud-beihangsoft-cn.e1.buaa.edu.cn/` 或者 `https://cloud-beihangsoft-cn.e2.buaa.edu.cn/`）直接登录。如果访问其中一个地址响应较慢，可以更换为另一个。

### 获取虚拟机

登录云平台后，在左侧“实验虚拟机”中查看自己的实验虚拟机。

### 登录虚拟机

通过[资源访问VPN](https://d.buaa.edu.cn/) `https://d.buaa.edu.cn`来访问实验虚拟机。选择“ssh”，输入虚拟机IP地址即可。

---

## PaaS简介

### 回顾——IaaS

>IaaS（Infrastructure as a Service），即基础设施即服务。指把IT基础设施作为一种服务通过网络对外提供，并根据用户对资源的实际使用量或占用量进行计费的一种服务模式。
>IaaS通过虚拟化技术提供云计算基础架构。包括服务器、网络和存储等。这些云服务器通常通过仪表盘或API提供给客户端，IaaS客户端可以完全控制整个基础架构。 IaaS提供与传统数据中心相同的技术和功能，而无需对其进行物理上的维护或管理。

通俗地讲，IaaS供应商主要提供了硬件设备。用户无需关心机房如何建设、服务器如何组装、网络如何铺设等等问题，只需要向IaaS供应商提出自己需要的配置，即可得到所需要的硬件资源。至于用户使用这些硬件资源部署什么操作系统、操作系统中安装什么软件则不在IaaS供应商的考虑范围内（尽管为了用户的方便考虑，绝大多数IaaS供应商会依据用户的选择，帮助用户将操作系统安装完毕后再交付）。

而PaaS用户需要关心的事情更少。

### PaaS

>PaaS （Platform as a Service），即平台即服务。指把服务器平台或者开发环境作为服务进行的一种服务模式。

在IaaS的基础上，额外提供操作系统、中间件、软件运行时等，即为PaaS。一般来说，PaaS为开发人员提供了一个框架，使他们可以基于PaaS环境创建自定义应用程序，而无需考虑硬件设备、操作系统以及运行环境的配置。

接下来的实验中，我们将陆续学习Docker及Kubernetes的基本使用方法，最终构建出一个较完整的PaaS平台。

## 实验

### [实验一 Docker的安装使用](/CCLab1-Docker.md)

### [实验二 Kubernetes的安装及集群初始化](/CCLab2-Kubernetes_Init.md)

### [实验三 Kubernetes的基本使用](/CCLab3-Kubernetes.md)

### [Kubernetes常见问题解答](/FAQ.md)
