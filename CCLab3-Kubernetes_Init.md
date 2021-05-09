# 实验二 Kubernetes集群构建

[toc]

---

## 写在前面

由于实验涉及到集群的管理与应用，步骤较多，可能遇到的问题和引起问题的原因也较多。请大家在进行实验时仔细阅读实验手册，这些手册都是助教们一行一行写出来的。另外，常见问题总结在[这里](http://dockerlab.roycent.cn/FAQ.html)，遇到了问题可以先在这个页面上查找原因及解决办法，这个页面会根据大家的实验情况进行更新。也建议大家在[这里](http://dockerlab.roycent.cn)阅读html格式的实验报告，以减少转换为PDF的过程中因页面格式造成的阅读困扰。同时，建议大家提前进行实验，以免在deadline遇到难以解决的问题。**两个Kubernetes实验的内容含量差异较大，实验三内容较多，请合理安排使用时间**。最后，祝大家顺利完成实验，在实验中学习到有用的知识。

实验三和实验四

最后，祝大家顺利完成实验，在实验中学习到有用的知识。

## 实验目的

- 了解Kubernetes的用途、运行原理
- 了解Kubernetes的常见应用场景
- 掌握Kubernetes中的重要概念
- 掌握Kubernetes集群的基本构建方法

## Kubernetes简介

> [*Kubernetes*](https://kubernetes.io/)是用于自动部署，扩展和管理容器化应用程序的开源系统。

之前我们提到过在PaaS系统中容器的作用及重要性。容器给开发和部署服务带来了极大的便利，但同时也带了一些问题。比如，如何处理大规模容器的部署、伸缩、故障转移、负载均衡等等问题。这些问题似乎在小规模使用容器（比如完成一项课程设计或者搭建一个个人网站）时都可以得到较为妥善的解决（或者根本不需要解决，比如自己的网站挂了也不会有什么影响，多数时候重启一下恢复了），但是在实际的生产环境中，这些都是关系到企业能否提供高效可靠服务的关键所在。

*Kubernetes*在希腊语中的含义是船长/领航员，这个名字生动地体现了它在容器**集群**管理中的作用——调度和监控全局的集装箱（*container*，容器）。Kubernetes主要具有以下几点功能：

- 服务发现和负载均衡
- 存储编排
- 自动部署和回滚
- 自动二进制打包
- 自我修复
- 密钥与配置管理

>Kubernetes不仅支持Docker容器，还支持其他的容器，比如Rtk容器。

## Kubernetes架构、组件、重要概念

在开始使用Kubernetes之前，为了更加顺畅地理解接下来的操作，有一些概念需要提前了解。本节仅解释最基本的概念，而不会讲解详细逻辑、底层架构、解决方案、设计思想等等。另外的一些重要概念，会在涉及到的时候进行补充。Kubernetes中的大部分概念，都可以被看作一种资源对象，几乎所有资源的对象都可以通过Kubernetes提供的kubectl工具（或者调用相应的API）来进行操作。

### Pod

Pod是Kubernetes中的基本执行单元，即管理、创建、计划的最小单元（而**不是**容器）。一个Pod中可以包含多个容器。在英文中，*pod*的意思是“豆荚”，豆荚和豆子的关系就类似于Pod和容器的关系。Pod内的各个容器共享网络和存储。比如可以使用localhost互相通信。

Pod的运行阶段(phase)是Pod在其生命周期中的简单宏观概述。phase可能的值会有：

- Pending：挂起。Pod已经被Kubernetes系统接受，但有一个或者多个容器镜像尚未创建。比如在调度Pod或者通过网络下载镜像时，可能会花费一定时间。在这个时候，Pod的状态就是Pending。
- Running：运行中。该Pod已经绑定到了一个节点上，Pod中所有的容器都已经被创建。至少有一个容器正在运行，或者正在处于重启或启动状态。
- Succeeded：成功。Pod中所有容器都被终止，并且不会再重启。
- Failed：失败。Pod中的所有容器都已终止了，并且至少有一个容器是因为失败终止。
- Unknown：未知。因为某些原因无法取得Pod的状态，通常是因为Pod与所在主机通信失败。

### Cluster

由于对故障转移、负载均衡等等功能的需要，容器往往不是运行在单一服务器上，而是运行在一个Cluster（集群）上。这也是Kubernetes诞生的重要原因之一。集群中会有很多的服务器（物理机或者虚拟机），这些服务器就被称为集群的节点（Node）。

### Node

Kubernetes一般都会运行在多个Node上，从而组成集群。Node是Pod真正运行的主机（这个主机既可以是物理机也可以是虚拟机）。Kubernetes并不会“创建”Node，而只是管理Node上的资源。在这些节点中会有负责管理集群的节点控制器，一般称之为master节点。在下文中，会以master节点代指运行节点控制器的节点，以普通节点或者node节点来代指其他节点。

### Service

在Kubernetes中，容器往往会频繁地重启或者销毁，如果按照Docker那样只用固定的IP地址和端口来访问不太现实。因此Kubernetes Service向外界提供了一种访问Pod的方式。Service有自己的IP地址和端口，同时也会为分配Pod的网络流量，达到负载均衡的效果。

### Namespace

Namespace用于将系统内部的对象划分为不同的组，实际上常用于隔离项目或者用户。比如Kubernetes自带服务一般运行在`kube-system`这个命名空间中。如果在创建Pod时不指定命名空间，创建出的Pod会存在于`default`这个命名空间中。我们在获取Pod列表时，如果不添加`--namespace`参数，则只会获取`default`命名空间中的Pod。

### Controller

一般情况下，Kubernetes通过控制器来管理Pod的生命周期。控制器用以监控集群的公共状态，并将当前的状态转变为期望的状态。比如，Controller中可能会定义Pod的部署属性：这个Pod有几个副本、在什么样的Node上运行等。Kubernetes提供了多种内置的控制器，比如Deployment、ReplicaSet、DaemonSet、StatefulSet、Job等等。当然，用户也可以编写自己的规则，来设计自己的专有控制器。

### Label

Label是Kubernetes中另一个核心概念。一个标签是一个`key=value`的键值对，其中key与value都由用户自己制定。Label可以被附加到各种资源对象上，比如刚刚提到的Node、Controller、Service等。我们可以通过给指定的资源对象捆绑一个或多个不同的Label来实现多维度的资源分组管理功能，以便灵活、方便地进行资源分配、调度、配置、部署等管理工作。Label的最常见的用法便是通过spec.selector来指定Label，从而Kubernetes寻找到所有包含你指定Label的对象、进行管理。也就是我们刚刚在配置文件中的用法。

## 安装Kubernetes

>**分发给大家的虚拟机已经安装好了Kubernetes并下载好了相关镜像。使用实验虚拟机进行实验无需进行本步操作**。下文会用`k8s`指代Kubernetes，二者为同一含义。并且在使用的命令中，有时会省略`sudo`或切换为`root`用户的操作，如无特殊提及，请按照实际情况选择是否使用`sudo`。从本节开始，**如无特殊说明，以`~$`开头的为命令，其他的为输出**。

需要注意的是，Kubernetes要求**CPU核心数至少为2**,每个版本的Kubernetes仅支持较少版本的Dokcer。因此，要使用CPU核心数足够的机器。在安装前，要**检查已安装的Docker版本和将要安装的Kubernetes版本是否兼容**，以免造成安装失败的情况。具体兼容/依赖情况可以在GitHub上的[Kubernetes Releases](https://github.com/kubernetes/kubernetes/releases)中，查看所需版本对应的Change Log。本节以使用两台2核心CPU、4G内存、已经安装了`Docker 18.06`的Ubuntu16.04虚拟机，安装部署`Kubernetes 1.13.2`为例。

- 添加k8s安装源
  
  ```command
  curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
  cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
  deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
  EOF
  ```

- 安装指定版本的k8s
`$ sudo apt update`
`$ sudo apt install kubernetes-cni=0.6.0-00`
`$ sudo apt install -y kubelet=1.13.2-00 kubeadm=1.13.2-00 kubectl=1.13.2-00`

> 由于使用的镜像源问题，kubernetes-cni这个依赖需要提前手动安装。同时，由于Kubernetes镜像存储在Google的服务器中而不是Docker Hub，所以我们需要下载Dokcer Hub上的相同镜像，并手动标记。
> **注意：下载镜像时要选择所需要的版本**

- 下载容器镜像文件并标记

  ```shell
  docker pull mirrorgooglecontainers/kube-apiserver-amd64:v1.13.2
  docker pull mirrorgooglecontainers/kube-controller-manager-amd64:v1.13.2
  docker pull mirrorgooglecontainers/kube-scheduler-amd64:v1.13.2
  docker pull mirrorgooglecontainers/kube-proxy-amd64:v1.13.2
  docker pull mirrorgooglecontainers/pause:3.1
  docker pull mirrorgooglecontainers/etcd-amd64:3.2.24
  docker pull coredns/coredns:1.2.6

  docker tag docker.io/mirrorgooglecontainers/kube-proxy-amd64:v1.13.2 k8s.gcr.io/kube-proxy:v1.13.2
  docker tag docker.io/mirrorgooglecontainers/kube-scheduler-amd64:v1.13.2 k8s.gcr.io/kube-scheduler:v1.13.2
  docker tag docker.io/mirrorgooglecontainers/kube-apiserver-amd64:v1.13.2 k8s.gcr.io/kube-apiserver:v1.13.2
  docker tag docker.io/mirrorgooglecontainers/kube-controller-manager-amd64:v1.13.2 k8s.gcr.io/kube-controller-manager:v1.13.2
  docker tag docker.io/mirrorgooglecontainers/etcd-amd64:3.2.24  k8s.gcr.io/etcd:3.2.24
  docker tag docker.io/mirrorgooglecontainers/pause:3.1  k8s.gcr.io/pause:3.1
  docker tag docker.io/coredns/coredns:1.2.6  k8s.gcr.io/coredns:1.2.6
  ```

## 构建集群前的准备工作

本小节的准备工作要在**所有节点**上执行。

> 由于Kubernetes不支持`swap`，在安装前要将`swap`关闭。

- 关闭swap
  `$ sudo swapoff -a`

> 为了方便辨认各个节点，更改hostname，同时将hostname加入`/etc/hosts`文件中。注意，hostname要重启终端（对于SSH用户来说即为重新登陆）才能在终端得以体现。

- 切换为root用户(**重要：不切换为root用户可能会带来问题。如果你真的没切换为root用户就更改了hostname，可以查看FAQ来获取解决办法。但是这么长一句写在实验手册里的话都没看到，可能你也不会去看FAQ，那么你就挂在了第一步。**)
  `$ sudo su`
- 更改hostname（选取其中一台为master节点，另一台为普通node节点，两台分别改为master和node并带上学号）
  `$ sudo hostnamectl set-hostname k8s-master`
- 更改hosts
  `$ sudo vim /etc/hosts`
  然后添加一行`127.0.0.1 k8s-master`或直接在已有的`127.0.0.1`后加入主机名
- 切换回普通用户（可选）
  `$ exit`

>为了简化实验步骤，我们直接禁用防火墙。注意，在实际使用时（包括生产环境，也包括开发环境、自己的云主机等），请按需求设置防火墙规则。

- 关闭防火墙
  - `$ sudo ufw disable`

## 初始化master节点

> 此处开始在master节点上执行

- 使用kubeadm初始化master节点
  `$ sudo kubeadm init --apiserver-advertise-address MY_MASTER_ADDRESS --pod-network-cidr=10.244.0.0/16 --kubernetes-version=1.13.2`
  其中，`MY_MASTER_ADDRESS`要替换成自己master节点的IP。
- 初始化中使用到的三个参数：
  - `--apiserver-advertise-address`选择master节点与其他节点通信所使用的interface。
  - `--pod-network-cidr`设置pod网络的范围。在此处我们使用了`10.244.0.0/16`这个范围，是因为在之后的过程中，我们会使用`flannel`配置网络，而`flannel`的默认配置文件中使用的就是这个范围。如果要更改使用其他网络方案或地址范围，此处要和之后配置网络时的配置文件一致。
  - `--kubernetes=version`选择安装Kubernetes的版本。由于网络原因，我们使用的是提前下载好的镜像文件，因此此处要与之前下载的镜像版本相匹配。

> 初始化失败的解决方法：
>
> - 重置master节点
>   `$ sudo kubeadm reset`
> - 重新运行之前的初始化命令，并添加`--v=6`参数来获得更详细的日志。

- 成功安装后，会显示如下信息：
  
  ```command
  Your Kubernetes master has initialized successfully!

  To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

  You should now deploy a pod network to the cluster.
  Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

  You can now join any number of machines by running the following on each node
  as root:

  kubeadm join MASTER_IP_ADDRESS:6443 --token qpve0s.mea7by3vzoffier2 --discovery-token-ca-cert-hash sha256:368a6bdf9120d72cf1baf8872010a6b188ad56e7d21f6702e03b5180cdc86165
  ```

这部分信息十分贴心地提示了接下来要做的事情：配置`kubectl`、配置网络、添加其他节点。最后一行为添加其他节点时使用的命令，它包含了所需的token以及证书hash值。这些信息可以复制下来，因为在下一步就会用到。

## 配置kubectl

配置kubectl的过程很简单，将上一步初始化成功信息中的命令复制过来执行即可。**注意:不要使用root用户执行这些命令。**

```command
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

为了方便使用，可以设置`kubectl`的命令补全功能：
`$ echo "source <(kubectl completion bash)" >> ~/.bashrc`
`$ source ~/.bashrc`

## 安装Pod网络

Kubernetes支持多种网络方案，本小节选用flannel。在配置pod网络前，要使master节点处于连网状态。使用flannel提供的配置文件来配置网络：
`$ sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/62e44c867a2846fefb68bd5f178daf4da3095ccb/Documentation/kube-`
`flannel.yml`

如果因网络问题无法下载这个文件，可以将链接替换成 `http://dockerlab.roycent.cn/kube-flannel.yml`

成功后会创建flannel网络：

```command
podsecuritypolicy.extensions/psp.flannel.unprivileged created
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.extensions/kube-flannel-ds-amd64 created
daemonset.extensions/kube-flannel-ds-arm64 created
daemonset.extensions/kube-flannel-ds-arm created
daemonset.extensions/kube-flannel-ds-ppc64le created
daemonset.extensions/kube-flannel-ds-s390x created
```

> 如果在初始化master时设置的网络范围不是flannel的默认范围`10.244.0.0/16`，则在此处要先下载配置文件，更改第127行的网络配置，以保持一致。

## 添加其他节点

仅仅有一个节点来运行Pod是不够的，无法体现出集群的各种特性。但是出于安全考虑，使用kubeadm初始化的Kubernetes集群默认不会将Pod调度到master节点上。因此，需要更改一些配置来使得master节点来运行Pod。执行命令：
`$ sudo kubectl taint nodes MASTER_NAME node-role.kubernetes.io/master-`
其中的`MASTER_NAME`可以通过`sudo kubectl get nodes`获取并替换。

>**污点与容忍**
>
>污点（*taint*）与容忍（*toleration*）是Kubernetes中的两个概念。其中污点是节点的属性，容忍是Pod的属性。
>
>之前提到Pod不会调度到master节点上，就是因为master节点默认会被打上`node-role.kubernete.io/master:NoSchedule`（禁止调度）的污点。除了`NoSchedule`外，节点的污点可以为空（即无污点），也可以为`PreferNoSchedule`（尽量不要调度）以及`NoExecute`（不会调度并且立即驱逐Pod）。Pod也可以选择是否容忍节点的污点，比如“不容忍污点为`PreferNoSchedule`的节点”，那么这个Pod就不会被调度到`PreferNoSchedule`的节点上。通过对污点与容忍的设置，可以让Pod避开某些节点或者将pod将某些节点中驱除。实际上的污点与容忍的使用十分灵活，达到的效果也十分多样，参见[Kubernetes文档](https://kubernetes.io/zh/docs/concepts/configuration/taint-and-toleration/)。
>
>**亲和与互斥**
>
>和污点与容忍的效果类似，Pod之间也有类似的属性，称为亲和与互斥。比如，某个Pod不希望与目标Pod运行在同一个节点上；或者某个Pod希望与目标Pod运行在同一节点上。

- 切换到node节点上，执行初始化master节点成功后的提示的命令：
  `$ sudo kubeadm join MASTER_IP_ADDRESS:6443 --token qpve0s.mea7by3vzoffier2 --discovery-token-ca-cert-hash sha256:368a6bdf9120d72cf1baf8872010a6b188ad56e7d21f6702e03b5180cdc86165`

>提示：由于在配置flannel网络时需要下载镜像，因此node节点在加入集群前也需要连网。

  ```command
  This node has joined the cluster:
  * Certificate signing request was sent to apiserver and a response was received.
  * The Kubelet was informed of the new secure connection details.

  Run 'kubectl get nodes' on the master to see this node join the cluster.
  ```

- 切换到master节点，查看节点
  
  ```command
  buaa@k8s-master:~$ sudo kubectl get nodes
  NAME       STATUS     ROLES    AGE   VERSION
  buaasoft   Ready      master   85m   v1.13.2
  k8s-node   Ready   <none>   71s   v1.13.2
  ```

至此，Kubernetes集群基本构建完成

>如果显示node节点状态为NotReady:
>查看pod列表：
>`$ sudo kubectl get pod --all-namespaces`
>
>```command
>NAMESPACE     NAME                               READY   STATUS                  RESTARTS   AGE
>kube-system   coredns-86c58d9df4-2cmp7           1/1     Running                 0          85m
>kube-system   coredns-86c58d9df4-nld9j           1/1     Running                 0          85m
>kube-system   etcd-buaasoft                      1/1     Running                 0          84m
>kube-system   kube-apiserver-buaasoft            1/1     Running                 0          84m
>kube-system   kube-controller-manager-buaasoft   1/1     Running                 1          84m
>kube-system   kube-flannel-ds-amd64-k5swh        0/1     Init:ImagePullBackOff   0          98s
>kube-system   kube-flannel-ds-amd64-rz2s4        1/1     Running                 0          24m
>kube-system   kube-proxy-98lzx                   1/1     Running                 0          98s
>kube-system   kube-proxy-zs7cc                   1/1     Running                 0          85m
>kube-system   kube-scheduler-buaasoft            1/1     Running  
>```
>
>可以看到有一个Pod并没有就绪。可以通过`describe`指令来查看pod的具体情况：
>`$ sudo kubectl describe pod POD_NAME --namespace=NAME_SPACE`
>然后根据Pod的具体情况进行排查。
>
>经常出现的情况：另一个节点没有联网

## 运行一个镜像

- 运行一个Nginx镜像
  `$ sudo kubectl run nginx-test --image=nginx --replicas=2`
- 查看创建结果
  `$ sudo kubectl get deployment nginx-test`

  ```command
  NAME         READY   UP-TO-DATE   AVAILABLE   AGE
  nginx-test   2/2     2            2           114s
  ```

> 注意：这个创建过程可能会持续几十秒。如果持续的时间过长，参见前一节的方法来排查问题。

- 查看Pod
  `$ sudo kubectl get pod -o wide`
  
  ```command
  NAME                          READY   STATUS    RESTARTS   AGE    IP            NODE       NOMINATED NODE   READINESS GATES
  nginx-test-59df8dcb7f-4sw5g   1/1     Running   0          3m3s   10.244.1.36   k8s-node   <none>           <none>
  nginx-test-59df8dcb7f-txlnq   1/1     Running   0          3m3s   10.244.0.15   buaasoft   <none>           <none>
  ```

在上面的信息中，我们获得了两个地址，通过这两个地址可以访问到Nginx容器。这个地址在Kubernetes集群之外是无法访问的，只能通过Kubernetes集群中的节点访问。当然，Kubernetes提供了向外提供服务的方法，之后我们会学习并使用。

- 验证是否可以访问Nginx
  `$ curl 10.244.1.36`

  ```html
  <!DOCTYPE html>
  <html>
  <head>
  <title>Welcome to nginx!</title>
  <style>
      body {
          width: 35em;
          margin: 0 auto;
          font-family: Tahoma, Verdana, Arial, sans-serif;
      }
  </style>
  </head>
  <body>
  <h1>Welcome to nginx!</h1>
  <p>If you see this page, the nginx web server is successfully installed and
  working. Further configuration is required.</p>

  <p>For online documentation and support please refer to
  <a href="http://nginx.org/">nginx.org</a>.<br/>
  Commercial support is available at
  <a href="http://nginx.com/">nginx.com</a>.</p>

  <p><em>Thank you for using nginx.</em></p>
  </body>
  </html>
  html
  ```

> `replicas`参数
> `replicas`参数会指定Pod的副本数。也就是说，如果指定`replicas=2`，那么Kubernetes就会一直使创建的Pod数量保持为2，即便其中有一个Pod因为异常退出。

可以看到，pod分别在两个节点上运行。

>和上一节的情况相似，此处的pod的状态也可能不是`Running`。依旧可以通过`describe`指令来排查原因。

## 安装图形化界面管理工具

常用的Kubernetes集群监控/管理工具有以下几种

- Kubernetes Dashboard
- Heapster
- Prometheus Operator
- Weave Scope

其中，Kubernetes Dashboard通过`d.buaa.edu.cn`访问会有错误、Heapster项目已终止、Prometheus Operator安装配置较为复杂，因此我们选择Weave Scope。感兴趣的同学可以尝试安装Promethus Operator。

- 安装Weave Scope
  `$ sudo kubectl apply -f http://dockerlab.roycent.cn/scope.yml`

安装成功后，Weave Scope会被部署到31721端口。访问该端口即可。
![scope_containers](/img/scope_containers.png)

在Weave Scope中，可以清楚地看到各个节点、控制器、容器之间的关系及他们的状态，也可以直接在节点上执行命令。Scope也支持对容器、部署、集群等的基本操作。
![scope_console](/img/scope_console.png)

## 动手做

>**请完整记录实验过程**

### 复现前文实验过程

- 完成双节点Kubernetes集群的初始化工作
- 运行一个Pod。要满足以下几点要求
  - 两台虚拟机的`hostname`带有自己的学号
  - 在master节点和node节点上均有Pod运行
- 安装Weave Scope或Prometheus Operator并体验其提供的部分功能。
