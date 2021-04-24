# 云计算实验指南

---

## 如何进行实验

### 访问云平台

可以通过[北航软件学院云平台](https://scs.buaa.edu.cn/)直接登录。

### 获取虚拟机

登录云平台后，在左侧“实验虚拟机”中查看自己的实验虚拟机。

### 登录虚拟机

- 使用SSH（终端直接使用SSH，或使用SSH客户端）登录虚拟机。注意，实验虚拟机只能在校内访问。
- 通过[资源访问VPN](https://d.buaa.edu.cn/) `https://d.buaa.edu.cn`来访问实验虚拟机。选择“ssh”，输入虚拟机IP地址即可。

### 通过认证连接网络

- 使用curl命令
  `curl -k -d "action=login&username=账号&password=密码&type=2&n=117&ac_id=1" "https://gw.buaa.edu.cn/cgi-bin/srun_portal"` 将其中的账号与密码替换为自己的校园网登录账号密码即可
- 使用本仓库`other/beihangLogin`脚本
  使用方法见[这里](how_to_use_beihanglogin.md)
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

### [实验四 构建简易的SaaS平台](/CCLab4-SaaS.md)

### [常见问题解答](/FAQ.md)

## Contribution

如果你发现了本仓库中的任何问题（typo、无效链接、知识性错误等等），欢迎提Issues和PR。
