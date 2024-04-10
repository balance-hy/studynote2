# Docker

有没有一种技术能够避免部署对服务器环境的依赖，减少复杂的部署流程呢？

答案是肯定的，这就是我们今天要学习的**Docker**技术。你会发现，有了Docker以后项目的部署如丝般顺滑，大大减少了运维工作量。

即便你对Linux不熟悉，你也能**轻松部署各种常见软件、Java项目**。

## 安装Docker

### 卸载旧版

首先如果系统中已经存在旧的Docker，则先卸载：

```Shell
yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
```

### 配置Docker的yum库

首先要安装一个yum工具

```Bash
yum install -y yum-utils
```

安装成功后，执行命令，配置Docker的yum源：注意下面的官方源无法连接

```Bash
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

换成阿里源

```shell
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

### 安装Docker

最后，执行命令，安装Docker

```Bash
yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 启动和校验

```Bash
# 查看docker版本
docker -v
# 列出本地系统上的 Docker 镜像
docker images
# 输出Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
# docker是一直运行的进程，上面的话代表还未启动docker

# 启动Docker  systemctl ： system control 系统控制
systemctl start docker

# 停止Docker
systemctl stop docker

# 重启
systemctl restart docker

# 设置开机自启
systemctl enable docker

# 执行docker ps命令，列出正在运行的 Docker 容器的信息 如果不报错，说明安装启动成功
docker ps
```

### 配置镜像加速

以阿里云镜像加速为例。

在首页的产品中，找到阿里云的**容器镜像服务**：

![aliyun](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/aliyun.png)

点击后进入控制台：

![aliyun2](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/aliyun2.png)

首次可能需要选择立刻开通，然后进入控制台。

找到**镜像工具**下的**镜像**加速器：

![aliyun3](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/aliyun3.png)

页面向下滚动，即可找到配置的文档说明：

![aliyun4](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/aliyun4.png)

具体命令如下： root权限

```shell
# 创建目录
mkdir -p /etc/docker

# 复制内容，注意把其中的镜像加速地址改成你自己的
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://7bfgwi9k.mirror.aliyuncs.com"]
}
EOF

# 重新加载配置
systemctl daemon-reload

# 重启Docker
systemctl restart docker
```

## 快速入门

### Mysql安装

```shell
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql
```

![image-20240410134345531](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410134345531.png)

这就完成了安装，再也不需要之前手动配置环境了

Docker会自动搜索并下载MySQL。注意：这里下载的不是安装包，而是镜像。**镜像中不仅包含了MySQL本身，还包含了其运行所需要的环境、配置、系统级函数库。因此它在运行时就有自己独立的环境，就可以跨系统运行**，也不需要手动再次配置环境了。这套**独立运行的隔离环境**我们称为**容器。**

说明：

- 镜像：英文是image
- 容器：英文是container

> 因此，Docker安装软件的过程，就是自动搜索下载镜像，然后创建并运行容器的过程。

Docker会根据命令中的镜像名称自动搜索并下载镜像，那么问题来了，**它是去哪里搜索和下载镜像的呢？这些镜像又是谁制作的呢？**

Docker官方提供了一个专门管理、存储镜像的网站，并对外开放了镜像上传、下载的权利。Docker官方提供了一些基础镜像，然后各大软件公司又在基础镜像基础上，制作了自家软件的镜像，全部都存放在这个网站。这个网站就成了Docker镜像交流的社区：

> [Docker hub](https://hub.docker.com/)

基本上我们常用的各种软件都能在这个网站上找到，我们甚至可以自己制作镜像上传上去。

> Docker本身包含一个后台服务，我们可以利用Docker命令告诉Docker服务，帮助我们快速部署指定的应用。Docker服务部署应用时，首先要去搜索并下载应用对应的镜像，然后根据镜像创建并允许容器，应用就部署完成了。

![whiteboard_exported_image](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/whiteboard_exported_image.png)

## Docker基础

### 常见命令

> [Use the Docker command line | Docker Docs](https://docs.docker.com/engine/reference/commandline/cli/)

| **命令**       | **说明**                               | **文档地址**                                                 |
| :------------- | :------------------------------------- | :----------------------------------------------------------- |
| docker pull    | 拉取镜像                               | [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) |
| docker push    | 推送镜像到DockerRegistry               | [docker push](https://docs.docker.com/engine/reference/commandline/push/) |
| docker images  | 查看本地镜像                           | [docker images](https://docs.docker.com/engine/reference/commandline/images/) |
| docker **rmi** | 删除本地镜像                           | [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) |
| docker run     | **创建下载并运行**容器（不能重复创建） | [docker run](https://docs.docker.com/engine/reference/commandline/run/) |
| docker stop    | 停止指定容器                           | [docker stop](https://docs.docker.com/engine/reference/commandline/stop/) |
| docker start   | 启动指定容器                           | [docker start](https://docs.docker.com/engine/reference/commandline/start/) |
| docker restart | 重新启动容器                           | [docker restart](https://docs.docker.com/engine/reference/commandline/restart/) |
| docker **rm**  | 删除指定容器                           | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/rm/) |
| docker ps      | 查看容器状态                           | [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) |
| docker logs    | 查看容器运行日志                       | [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) |
| docker exec    | 进入容器                               | [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) |
| docker save    | 保存镜像到本地压缩文件                 | [docker save](https://docs.docker.com/engine/reference/commandline/save/) |
| docker load    | 加载本地压缩文件到镜像                 | [docker load](https://docs.docker.com/engine/reference/commandline/load/) |
| docker inspect | 查看容器详细信息                       | [docker inspect](https://docs.docker.com/engine/reference/commandline/inspect/) |

```shell
docker run -d \ # 创建并运行容器  -d 代表在后台运行，如果不加会卡在那里，无法做其他事  \ 换行符
  --name mysql \ # --name 给容器起名
  -p 3306:3306 \ # -p 端口映射 第一个3306为宿主机端口，第二个3306为容器端口 无法直接访问容器
  -e TZ=Asia/Shanghai \ # -e 环境变量 key=value
  -e MYSQL_ROOT_PASSWORD=123456 \
  mysql # 镜像名 正常应该 mysql:5.7 指定想要拉取的镜像版本
  
# 保存镜像到本地 
docker save -o ngink.tar nginx:latest
# 读取本地镜像  
docker load -i nginx.tar
```

![image-20240410141114448](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240410141114448.png)

#### 演示

以Nginx为例演示常用命令

```shell
# 第1步，去DockerHub查看nginx镜像仓库及相关信息

# 第2步，拉取Nginx镜像
docker pull nginx

# 第3步，查看镜像
docker images
# 结果如下：
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
nginx        latest    605c77e624dd   2 years ago   141MB
mysql        latest    3218b38490ce   2 years ago   516MB

# 第4步，创建并允许Nginx容器
docker run -d --name nginx -p 80:80 nginx

# 第5步，查看运行中容器
docker ps
# 也可以加格式化方式访问，格式会更加清爽
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第6步，访问网页，地址：http://虚拟机地址 即可访问
http://192.168.40.129/

# 第7步，停止容器
docker stop nginx

# 第8步，查看所有容器 -a 就是 all 的意思
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"

# 第9步，再次启动nginx容器 注意此时是start 因为已经存在容器，不需要run
docker start nginx

# 第10步，再次查看容器
docker ps

# 输出 nginx 运行日志
docker logs nginx
# 加上 -f 持续监听运行
docker logs -f nginx

# 第11步，查看容器详细信息
docker inspect nginx

# 第12步，进入容器,查看容器内目录 -it 进入容器终端 bash 命令行
docker exec -it nginx bash
# 或者，可以进入MySQL 后跟mysql的命令mysql -uroot -p 访问 mysql
docker exec -it mysql mysql -uroot -p
# 输入 exit 退出

# 第13步，删除容器
docker rm nginx
# 发现无法删除，因为容器运行中，强制删除容器 或者 先停掉运行中容器，再删即可
docker rm -f nginx
```

#### 简化常用命令

在使用 `docker ps` 命令时，常常需要格式化输出信息以便于阅读，但是格式化的代码太长，每次手敲太折磨

```shell
# 也可以加格式化方式访问，格式会更加清爽
docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"
```

我们可以通过给命令起别名的方式简化使用

我们只需要修改`/root/.bashrc` 文件即可

```shell
# 修改/root/.bashrc文件
vi /root/.bashrc
内容如下：
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"' # 新增别名
alias dis='docker images' # 新增别名

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi
```

**然后，执行命令使别名生效**

```shell
source /root/.bashrc
```

### 数据卷

