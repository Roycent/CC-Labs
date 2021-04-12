- 打开服务器管理器，选择添加角色或功能
  ![](img/win/1.png)
- 选择基于角色或功能的安装
  ![](img/win/2.png)
- 选择当前服务器
  ![](img/win/3.png)
- 选择AD域服务并确定
  ![](img/win/4.png)
- 依次下一步
  ![](img/win/5.png)
  ![](img/win/6.png)
  ![](img/win/7.png)
  ![](img/win/8.png)
- 将服务器设置为域控制器，点击上方警告后的“更多”
  ![](img/win/9.png)
- 点击上方消息后的“将此服务器提升为域控制器”
  ![](img/win/10.png)
- 填写根域名，由于不会使用到，实际上可以随意填写
  ![](img/win/11.png)
- 设置还原密码
  ![](img/win/13.png)
- 此处警告可忽略
  ![](img/win/14.png)
- 使用默认值即可
  ![](img/win/15.png)
- 依次下一步，并点击安装
  ![](img/win/16.png)
  ![](img/win/17.png)
  ![](img/win/18.png)
- 等待安装结束，域部分的配置到此结束。

---

配置Remote App相关的远程桌面功能。

- 打开服务器管理器，选择添加角色或功能，选择远程桌面服务安装
  ![](img/win/19.png)
- 类型选择快速启动
  ![](img/win/20.png)
- 选择基于会话的桌面部署
  ![](img/win/21.png)
- 选择本地服务器
  ![](img/win/23.png)
- 将需要时重启选项打勾，并点击部署
  ![](img/win/25.png)
- 部署成功后会自动重启
  ![](img/win/27.png)
- Windows Server基础配置到此结束。