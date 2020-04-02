# 如何登录校园网

>登录脚本代码来自Github goomadao的[beihangLogin](https://github.com/goomadao/beihangLogin)项目，使用Go 1.14编译。

- 通过`d.buaa.edu.cn`登录虚拟机，此步骤省略
- 点击上面的**文件管理**，依次打开`home`、`buaa`（当然，你也可以选择其他位置。为了方便这里放到了`~`文件夹中）。然后点击右上角的省略号，选择文件上传
  ![login1](/img/login_1.png)
- 选择`beihangLogin`文件并上传
  ![login2](/img/login_2.png)
- 上传完成后可以在文件夹中看到该文件
  ![login3](/img/login_3.png)
- 为这个文件增加可执行权限
  `chmod +x beihangLogin`
- 使用该脚本登录校园网。用法:`./beihangLogin login -u 学号 -p 密码`
  ![login4](/img/login_4.png)