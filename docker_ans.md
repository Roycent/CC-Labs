# CCLab1-Docker

> 学号：
> 姓名：

## 动手做

### 安装 Docker

### 下载 portainer 镜像

### 查看本地镜像列表

### 运行 portainer 容器

### 登录运行的 portainer 网站并查看 Dashboard 中的信息（使用 local 模式）

### 使用命令行搜索 Docker Hub 中的任一镜像

### 下载并安装 MySQL 镜像，并将数据库访问端口设置为 8006

### 在 MySQL 容器中创建文件并查看结果

### 在 MySQL 容器中执行命令并创建数据库

### 将修改过的 MySQL 容器保存为镜像

### 编写 Dockerfile，使用 Nginx 镜像显示一个静态网页，**网页内容添加上自己的学号**

### 构建并运行新建的镜像

### 将镜像上传至自己的 Docker Hub 中

## 综合实验

编写一个 Dockerfile，使用其构建出一个镜像并运行，满足以下要求：

1. 基础镜像使用 Nginx 镜像
2. 将 Nginx 镜像中的`/usr/share/nginx/html/index.html`文件替换为`http://dockerlab.roycent.cn/lab1.html`文件
3. 在镜像的`/usr/share/nginx/html/`文件夹中添加一个`page.html`文件，其内容为自己的学号
4. 运行该镜像，虚拟机的 80 端口映射为将容器的 80 端口，访问、查看网页并记录网页内容
5. 将该镜像上传至自己的 Docker Hub 中
