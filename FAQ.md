# FAQ

- 使用登录校园网脚本`login.sh`提示`password_algo_error`
  - 使用`beihangLogin`脚本，使用说明在[这里](/how_to_use_beihanglogin.md).
- 使用`d.buaa.edu.cn`时，无法切换RDP/HTTP/SSH选项
  - 清除浏览器缓存并强制刷新（比如Chrome的`ctrl+shift+R`）
- 为什么有三台虚拟机？都要加入集群吗？
  - 实验要求**至少**两台虚拟机组成集群，第三台虚拟机为备用机。如果你对自己有信心，使用三台虚拟机组成集群会获得更好的实验效果。
- 更改hostname后，执行sudo命令无反应
  - 开启两个终端（为了方便叙述，这里将他们称为`t1`、`t2`
  - `t1：echo $$`
  - `t2: pktty agent --process THE_NUBER` 其中的`THE_NUMBER`为上一步输出的数字
  - `t1: pkexec sudo vim /etc/hosts` 打开hosts文件，并将hostname写入其中（将hostname，例如`k8s-master-17370000`写到127.0.0.1 localhost后面）。在执行这个命令、打开vim之前，`t2`中可能会要求验证密码，在`t2`中输入密码验证即可。
- 节点加入集群时提示sha值错误。`cluster CA found in cluster-info configmap is invalid: public key`
  - 在master节点上重新获取sha：
    `openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'`
  - 然后重新执行`kubeadmin join`，命令中的sha值更换为上一步输出的值。
- 节点加入集群时提示`unable to fetch the kubeadm-config ConfigMap: failed to get config map: Unauthorized`
  原因：集群初始化时master节点提供的token是有效期的，token过期需要重新生成。在master节点执行`kubeadm token create`即可获得新节点
- 节点或Pod显示`NotReady`的自查方法
  - 使用`kubectl describe`命令查看对象状态
  - 使用`kubectl log`命令查看日志
  - 常见问题
    - 没联网
    - 没有关闭swap
    - 没有关闭防火墙或没有正确设置防火墙规则
- 在进行“故障转移”一节实验时，节点状态变为了`NotReady`，但Pod状态一直是`Running`
  - 目前没有很好的解决办法。对于这个情况，不少用户提出了Issue，比如[Issue #85895](https://github.com/kubernetes/kubernetes/issues/85895)、[Issue #82958](https://github.com/kubernetes/kubernetes/issues/82958)等等。Issue区也有评论说Node节点数量大于1时会解决这个问题。**如果你遇到了这个问题，可以直接跳过本节实验**。如果你找到了解决方法，请向助教提出，如果可行会有bonus
- 在“暴露端口”一节中，应用yaml文件报错
  - yaml文件有误，已经更正
