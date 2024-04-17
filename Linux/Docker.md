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

```
docker-ce：表示 Docker Community Edition（Docker 社区版）。这是 Docker 的开源版本，为用户提供容器化应用程序所需的功能和工具。

docker-ce-cli：表示 Docker Command Line Interface（Docker 命令行界面）。这是 Docker 命令行工具，用于与 Docker 引擎通信并执行 Docker 相关操作。

containerd.io：表示 containerd 容器运行时，它是 Docker 使用的低级容器运行时。containerd 可以作为 Docker 引擎的依赖项，也可以单独用于管理容器的运行。

docker-buildx-plugin：这是一个 Docker 插件，提供了构建跨平台 Docker 镜像的能力。buildx 是 Docker 提供的构建扩展工具，它允许你为多种平台和架构构建镜像。

docker-compose-plugin：这是 Docker 的 Compose 插件。Docker Compose 是一个用于定义和管理多容器 Docker 应用程序的工具。通过该插件，你可以在 Docker CLI 中使用 Compose 的功能。

命令中的 -y 是一个选项，表示在安装过程中自动确认所有提示，而不需要用户手动输入确认。这可以让安装过程在没有用户交互的情况下进行。
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

容器是隔离环境，容器内程序的文件、配置、运行时产生的容器都在容器内部，我们要读写容器内的文件非常不方便。思考几个问题：

- 如果要升级MySQL版本，需要销毁旧容器，那么数据岂不是跟着被销毁了？
- MySQL、Nginx容器**运行后**，如果我要**修改其中的某些配置该怎么办**？
- 我想要让Nginx**代理我的静态资源**怎么办？

因此，容器提供程序的运行环境，但是**程序运行产生的数据、程序运行依赖的配置都应该与容器解耦**。

#### 什么是数据卷

**数据卷（volume）**是一个虚拟目录，是**容器内目录**与**宿主机**目录之间映射的桥梁。

以Nginx为例，我们知道Nginx中有两个关键的目录：

- `html`：放置一些静态资源
- `conf`：放置配置文件

如果我们要让Nginx代理我们的静态资源，最好是放到`html`目录；如果我们要修改Nginx的配置，最好是找到`conf`下的`nginx.conf`文件。

但遗憾的是，**容器运行的Nginx所有的文件都在容器内部。所以我们必须利用数据卷将两个目录与宿主机目录关联**，方便我们操作。如图：

![whiteboard_exported_image2](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/whiteboard_exported_image2.png)

对照 windows

![image-20240411104245537](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240411104245537.png)

在上图中：

- 我们创建了两个数据卷：`conf`、`html`
- Nginx容器内部的`conf`目录和`html`目录分别与两个数据卷关联。
- 而数据卷conf和html分别指向了宿主机的`/var/lib/docker/volumes/conf/_data`目录和`/var/lib/docker/volumes/html/_data`目录

这样以来，容器内的`conf`和`html`目录就 与宿主机的`conf`和`html`目录关联起来，我们称为**挂载**。此时，我们操作宿主机的`/var/lib/docker/volumes/html/_data`就是在操作容器内的`/usr/share/nginx/html/_data`目录。只要我们将静态资源放入宿主机对应目录，就可以被Nginx代理了。

> `/var/lib/docker/volumes`这个目录就是默认的存放所有容器数据卷的目录，其下再根据数据卷名称创建新目录，格式为`/数据卷名/_data`。
>
> **为什么不让容器目录直接指向**宿主机目录呢？
>
> - 因为直接指向宿主机目录就与宿主机强耦合了，如果切换了环境，宿主机目录就可能发生改变了。由于容器一旦创建，目录挂载就无法修改，这样容器就无法正常工作了。
> - 但是容器指向数据卷，一个逻辑名称，而数据卷再指向宿主机目录，就不存在强耦合。如果宿主机目录发生改变，只要改变数据卷与宿主机目录之间的映射关系即可。
>
> 不过，我们通过由于数据卷目录比较深，不好寻找，通常我们也**允许让容器直接与宿主机**目录挂载而不使用数据卷

#### 数据卷相关命令

数据卷的相关命令有：

| **命令**              | **说明**             | **文档地址**                                                 |
| :-------------------- | :------------------- | :----------------------------------------------------------- |
| docker volume create  | 创建数据卷           | [docker volume create](https://docs.docker.com/engine/reference/commandline/volume_create/) |
| docker volume ls      | 查看所有数据卷       | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_ls/) |
| docker volume rm      | 删除指定数据卷       | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_prune/) |
| docker volume inspect | 查看某个数据卷的详情 | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/volume_inspect/) |
| docker volume prune   | 清除未使用的数据卷   | [docker volume prune](https://docs.docker.com/engine/reference/commandline/volume_prune/) |

可以使用 `docker volume --help`来查看有关于数据卷的所有命令

#### **演示nginx的html目录挂载**

>  在执行`docker run`命令时，使用`-v 数据卷：容器内目录`可以完成数据卷挂载。
>
> 创建容器时，如果挂载了数据卷且数据卷不存在，会自动创建数据卷
>
> 注意只能在创建容器时挂载！！！！！ 容器创建后无法挂载

```shell
# 1.首先创建容器并指定数据卷，注意通过 -v 参数来指定数据卷 注意第一个参数为宿主机数据卷名称 第二个参数为容器内文件地址
# 具体可查看镜像网站 具体镜像的帮助文档
docker run -d --name nginx -p 80:80 -v html:/usr/share/nginx/html nginx

# 2.然后查看数据卷
docker volume ls
# 结果
DRIVER    VOLUME NAME
local     34b6f257314826f15d1bb10df9418700fbfb05ae60b12c8212b871ef38559d96
local     html

# 3.查看数据卷详情
docker volume inspect html
# 结果
[
    {
        "CreatedAt": "2024-05-17T19:57:08+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/html/_data",
        "Name": "html",
        "Options": null,
        "Scope": "local"
    }
]

# 4.查看/var/lib/docker/volumes/html/_data目录
ls
# 可以看到与nginx的html目录内容一样，结果如下：
50x.html  index.html

# 5.进入该目录，并随意修改index.html内容
cd /var/lib/docker/volumes/html/_data
vi index.html

# 6.重启nginx，打开页面，查看效果
docker restart nginx

# 7.进入容器内部，查看/usr/share/nginx/html目录内的文件是否变化
docker exec -it nginx bash
```

#### MySQL的匿名数据卷

```shell
docker inspect mysql # 查看名为mysql的容器具体信息
```

我们发现在 config 中显示无数据卷

```json
"Config": {
............
    "Volumes": {
        "/var/lib/mysql": {}
.........
},
```

而在 Mounts 中有一个 长串未知 的数据卷, 但是**数据卷未定义**。这就是匿名卷。

```json
"Mounts": [
    {
        "Type": "volume",
        "Name": "34b6f257314826f15d1bb10df9418700fbfb05ae60b12c8212b871ef38559d96",
        "Source": "/var/lib/docker/volumes/34b6f257314826f15d1bb10df9418700fbfb05ae60b12c8212b871ef38559d96/_data",
        "Destination": "/var/lib/mysql",
        "Driver": "local",
        "Mode": "",
        "RW": true,
        "Propagation": ""
    }
],
```

可以发现，其中有几个关键属性：

- Name：数据卷名称。由于定义容器未设置容器名，这里的就是匿名卷自动生成的名字，一串hash值。
- Source：宿主机目录
- Destination : 容器内的目录

上述配置是将容器内的`/var/lib/mysql`这个目录，与数据卷`29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f`挂载。于是在宿主机中就有了`/var/lib/docker/volumes/29524ff09715d3688eae3f99803a2796558dbd00ca584a25a4bbc193ca82459f/_data`这个目录。这就是匿名数据卷对应的目录，其使用方式与普通数据卷没有差别。

#### mysql数据卷问题

可以发现，数据卷的目录结构较深且如果为自动生成的匿名卷，名字很长不方便记忆，如果我们去操作数据卷目录会不太方便。

**而且，因为mysql的数据是挂载到匿名卷中，如果我们需要版本迁移删除当前mysql的容器再次创建最新版本的mysql，虽然原来数据卷还在，但是会生成新的匿名卷，需要我们手动迁移，这很不方便。**

在很多情况下，我们会直接将容器目录与宿主机指定目录挂载。

即由我们来指定数据保存在哪里，而非默认保存在 `/var/lib/docker/volumes` 目录下

**挂载语法与数据卷类似：**

```shell
# 挂载本地目录
-v 本地目录:容器内目录
# 挂载本地文件
-v 本地文件:容器内文件
```

**注意**：本地目录或文件必须以 `/` 或 `./`开头，如果直接以名字开头，会被识别为数据卷名而非本地目录名。

> ```Bash
> -v mysql:/var/lib/mysql # 会被识别为一个数据卷叫mysql，运行时会自动创建这个数据卷
> -v ./mysql:/var/lib/mysql # 会被识别为当前目录下的mysql目录，运行时如果不存在会创建目录
> ```

**教学演示**，删除并重新创建mysql容器，并完成本地目录挂载：

- 挂载`/root/mysql/data`到容器内的`/var/lib/mysql`目录
- 挂载`/root/mysql/init`到容器内的`/docker-entrypoint-initdb.d`目录（**初始化的SQL脚本目录**）
- 挂载`/root/mysql/conf`到容器内的`/etc/mysql/conf.d`目录（**这个是MySQL配置文件目录**）

在课前资料中已经准备好了mysql的`init`目录和`conf`目录：我们直接将整个mysql目录上传至虚拟机的`/root`目录下：

```shell
# 1.删除原来的MySQL容器
docker rm -f mysql

# 2.进入root目录
cd ~

# 3.创建并运行新mysql容器，挂载本地目录
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  mysql

# 4.查看root目录，可以发现~/mysql/data目录已经自动创建好了
ls -l mysql
# 结果：
总用量 4
drwxr-xr-x. 2 root    root   20 5月  19 15:11 conf
drwxr-xr-x. 7 polkitd root 4096 5月  19 15:11 data
drwxr-xr-x. 2 root    root   23 5月  19 15:11 init

# 查看data目录，会发现里面有大量数据库数据，说明数据库完成了初始化
ls -l data
```

### 镜像

前面我们一直在使用别人准备好的镜像，那如果我要部署一个Java项目，把它打包为一个镜像该怎么做呢？

#### 镜像结构

镜像就是包含了程序以及程序运行需要的系统函数库、环境、配置、依赖等文件的文件包

构建镜像就是把上述文件打包的过程

举个例子，我们要**从0部署一个Java应用**，大概流程是这样：

- 准备一个linux服务（CentOS或者Ubuntu均可）
- 安装并配置JDK
- 上传Jar包
- 运行jar包

那因此，我们打包镜像也是分成这么几步：

- 准备Linux运行环境（java项目并不需要完整的操作系统，仅仅是基础运行环境即可）
- 安装并配置JDK
- 拷贝jar包
- 配置启动脚本

上述步骤中的每一次操作其实都是在生产一些文件（系统运行环境、函数库、配置最终都是磁盘文件），所以**镜像就是一堆文件的集合**。

但需要注意的是，镜像文件不是随意堆放的，而是**按照操作的步骤分层叠加而成**，每一层形成的文件都会单独打包并标记一个唯一id，称为**Layer**（**层**）。这样，**如果我们构建时用到的某些层其他人已经制作过，就可以直接拷贝使用这些层，而不用重复制作**。

例如，第一步中需要的Linux运行环境，通用性就很强，所以Docker官方就制作了这样的只包含Linux运行环境的镜像。我们在制作java镜像时，就无需重复制作，直接使用Docker官方提供的CentOS或Ubuntu镜像作为基础镜像。然后再搭建其它层即可，这样逐层搭建，最终整个Java项目的镜像结构如图所示：

![a29d03c9-53b3-4f0a-b13d-83b51e9c2db8](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/a29d03c9-53b3-4f0a-b13d-83b51e9c2db8.png)

#### Dockerfile

由于制作镜像的过程中，需要逐层处理和打包，比较复杂，所以Docker就提供了自动打包镜像的功能。我们只需要将打包的过程，每一层要做的事情用固定的语法写下来，交给Docker去执行即可。

而这种记录镜像结构的文件就称为**Dockerfile**，其对应的语法可以参考官方文档：

https://docs.docker.com/engine/reference/builder/

其中的语法比较多，比较常用的有：

| **指令**       | **说明**                                     | **示例**                     |
| :------------- | :------------------------------------------- | :--------------------------- |
| **FROM**       | 指定基础镜像                                 | `FROM centos:6`              |
| **ENV**        | 设置环境变量，可在后面指令使用               | `ENV key value`              |
| **COPY**       | 拷贝本地文件到镜像的指定目录                 | `COPY ./xx.jar /tmp/app.jar` |
| **RUN**        | 执行Linux的shell命令，一般是安装过程的命令   | `RUN yum install gcc`        |
| **EXPOSE**     | 指定容器运行时监听的端口，是给镜像使用者看的 | EXPOSE 8080                  |
| **ENTRYPOINT** | 镜像中应用的启动命令，容器运行时调用         | ENTRYPOINT java -jar xx.jar  |

例如，要基于Ubuntu镜像来构建一个Java应用，其Dockerfile内容如下：

```dockerfile
# 指定基础镜像
FROM ubuntu:16.04
# 配置环境变量，指定 JDK 安装目录为 /usr/local 指定容器内的时区为 Asia/Shanghai。
ENV JAVA_DIR=/usr/local
ENV TZ=Asia/Shanghai
# 拷贝jdk和java项目的包 注意docker-demo.jar为源文件路径 /tmp/app.jar为容器内路径
# ./jdk8.tar.gz 是主机上的文件路径，被拷贝到容器内的 $JAVA_DIR/ 目录。
COPY ./jdk8.tar.gz $JAVA_DIR/
COPY ./docker-demo.jar /tmp/app.jar
# 设定时区
# ln -snf /usr/share/zoneinfo/$TZ /etc/localtime 是软链接 /etc/localtime 到 $TZ 所指定的时区信息。
# echo $TZ > /etc/timezone 将时区信息写入 /etc/timezone 文件中。
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 安装JDK
# 切换到 JAVA_DIR 目录。
# 解压 jdk8.tar.gz 文件。
# 解压后，将目录 jdk1.8.0_144 重命名为 java8。
RUN cd $JAVA_DIR \
 && tar -xf ./jdk8.tar.gz \
 && mv ./jdk1.8.0_144 ./java8
# 配置环境变量 JAVA_HOME 是一个环境变量，指定 Java 安装目录为 $JAVA_DIR/java8。
# PATH 是一个环境变量，将 JAVA_HOME/bin 添加到 PATH 中，以便容器中的 Java 命令可被识别。
ENV JAVA_HOME=$JAVA_DIR/java8
ENV PATH=$PATH:$JAVA_HOME/bin
# 指定项目监听的端口
EXPOSE 8080
# 入口，java项目的启动命令
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

上面的还是太繁琐了，有人提供了基础的系统加JDK环境的镜像，实际上我们只需要指定 jar 包和命令

```dockerfile
# 基础镜像
FROM openjdk:11.0-jre-buster
# 设定时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 拷贝jar包 注意docker-demo.jar为源文件路径 /app.jar为容器内路径
COPY docker-demo.jar /app.jar
# 入口
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

#### 构建镜像

当Dockerfile文件写好以后，就可以利用命令来构建镜像了。

在课前资料中，我们准备好了一个demo项目及对应的Dockerfile：

首先，我们将课前资料提供的`docker-demo.jar`包以及`Dockerfile`拷贝到虚拟机的`/root/demo`目录：

然后，执行命令，构建镜像：

注意：如第一次构建，需要先下载基础镜像 `docker pull openjdk:11.0-jre-buster`

```shell
# 进入镜像目录
cd /root/demo
# 开始构建
docker build -t docker-demo:1.0 .
```

- `docker build `: 就是构建一个docker镜像
- `-t docker-demo:1.0` ：`-t`参数是指定镜像的名称（`repository`和`tag`）
- `.` : 最后的点是指构建时Dockerfile所在路径，由于我们进入了demo目录，所以指定的是`.`代表当前目录，也可以直接指定Dockerfile目录：

然后尝试运行该镜像：

在 Dockerfile 文件中定义的 `ENTRYPOINT ["java", "-jar", "/app.jar"]` 是用于指定容器的入口点命令。当容器启动时，Docker 会自动执行这个命令。这是 Docker 管理容器的标准方式之一。

```shell
# 1.创建并运行容器
docker run -d --name docker-demo -p 8080:8080 docker-demo:1.0
# 访问
http://192.168.40.129:8080/hello/count
```

### 网络

上节课我们创建了一个Java项目的容器，而Java项目往往需要访问其它各种中间件，例如MySQL、Redis等。现在，我们的容器之间能否互相访问呢？

首先，我们查看下MySQL容器的详细信息，重点关注其中的网络IP地址：

```shell
docker inspect mysql
# 发现 mysql 的网关为172.17.0.1 ip地址为172.17.0.3
docker inspect nginx
# 发现 nginx 的网关为172.17.0.1 ip地址为172.17.0.2
docker inspect docker-demo
# 发现 docker-demo 的网关为172.17.0.1 ip地址为172.17.0.4
```

好像有点规律，这其实是在安装docker时，docker会创建一个虚拟的网卡，网卡名为 docker 0，同时也会创建一个虚拟的网桥

默认情况下，所有容器都是以bridge方式连接到Docker的一个虚拟网桥上：

![image-20240411140058719](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240411140058719.png)

**但这会有一个问题，即容器的网络IP其实是一个虚拟的IP，其值并不固定与某一个容器绑定，而是自动分配的。如果我们在开发时写死某个IP，而在部署时很可能MySQL容器的IP会发生变化，连接会失败。**

我们必须借助于docker的**自定义网络功能**来解决这个问题，官方文档：

https://docs.docker.com/engine/reference/commandline/network/

**加入自定义网络的容器才可以通过容器名相互访问**，常见命令有：

| **命令**                  | **说明**                 | **文档地址**                                                 |
| :------------------------ | :----------------------- | :----------------------------------------------------------- |
| docker network create     | 创建一个网络             | [docker network create](https://docs.docker.com/engine/reference/commandline/network_create/) |
| docker network ls         | 查看所有网络             | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_ls/) |
| docker network rm         | 删除指定网络             | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_rm/) |
| docker network prune      | 清除未使用的网络         | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_prune/) |
| docker network connect    | 使指定容器连接加入某网络 | [docs.docker.com](https://docs.docker.com/engine/reference/commandline/network_connect/) |
| docker network disconnect | 使指定容器连接离开某网络 | [docker network disconnect](https://docs.docker.com/engine/reference/commandline/network_disconnect/) |
| docker network inspect    | 查看网络详细信息         | [docker network inspect](https://docs.docker.com/engine/reference/commandline/network_inspect/) |

演示：自定义网络

```shell
# 1.首先通过命令创建一个网络
docker network create hmall

# 2.然后查看网络
docker network ls
# 结果：
NETWORK ID     NAME      DRIVER    SCOPE
639bc44d0a87   bridge    bridge    local
403f16ec62a2   hmall     bridge    local
0dc0f72a0fbb   host      host      local
cd8d3e8df47b   none      null      local
# 其中，除了hmall以外，其它都是默认的网络

# 3.让dd和mysql都加入该网络，注意，在加入网络时可以通过--alias给容器起别名
# 这样该网络内的其它容器可以用别名互相访问！
# 3.1.mysql容器，指定别名为db，另外每一个容器都有一个别名是容器名
docker network connect hmall mysql --alias db
# 3.2.db容器，也就是我们的java项目
docker network connect hmall dd

# 4.进入dd容器，尝试利用别名访问db
# 4.1.进入容器
docker exec -it dd bash
# 4.2.用db别名访问
ping db
# 结果
PING db (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.070 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.056 ms
# 4.3.用容器名访问
ping mysql
# 结果：
PING mysql (172.18.0.2) 56(84) bytes of data.
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=1 ttl=64 time=0.044 ms
64 bytes from mysql.hmall (172.18.0.2): icmp_seq=2 ttl=64 time=0.054 ms
```

```shell
# 也可以一开始就指定加入的网络 就无需后面使用 docker network connect
docker run -d --name docker-demo -p 8080:8080 --network 网络名 docker-demo:1.0
```

## 项目部署

### 部署 java 项目

`hmall`项目是一个maven聚合项目，使用IDEA打开`hmall`项目，查看项目结构如图：

![image-20240412165823025](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240412165823025.png)

查看application.yaml，你会发现其中的JDBC地址并未写死，而是读取变量：

![image-20240412165959982](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240412165959982.png)

将项目打包，将打包生成的jar包和提供的dockfile上传到 /root 目录下

部署项目

```shell
# 1.构建项目镜像，不指定tag，则默认为latest
docker build -t hemall:1.0 .

# 2.查看镜像
docker images

# 结果


# 3.创建并运行容器，并通过--network将其加入hmall网络，这样才能通过容器名访问mysql
docker run -d --name hemall -p 8080:8080 --network hemall hemall:1.0
```

测试，通过浏览器访问：http://你的虚拟机地址:8080/search/list

### 部署前端项目

`hmall-portal`和`hmall-admin`是前端代码，需要基于nginx部署。在课前资料中已经给大家提供了nginx的部署目录：

![image-20240412172641237](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240412172641237.png)

其中：

- `html`是静态资源目录，我们需要把`hmall-portal`以及`hmall-admin`都复制进去
- `nginx.conf`是nginx的配置文件，主要是完成对`html`下的两个静态资源目录做代理

我们现在要做的就是把该目录上传到虚拟机的`/root`目录下：

然后创建nginx容器并完成两个挂载：

- 把`/root/nginx/nginx.conf`挂载到`/etc/nginx/nginx.conf`
- 把`/root/nginx/html`挂载到`/usr/share/nginx/html`

由于需要让nginx同时代理hmall-portal和hmall-admin两套前端资源，因此我们需要暴露两个端口：

- 18080：对应hmall-portal
- 18081：对应hmall-admin

命令如下：

```Bash
docker run -d \
  --name nginx \
  -p 18080:18080 \
  -p 18081:18081 \
  -v /root/nginx/html:/usr/share/nginx/html \
  -v /root/nginx/nginx.conf:/etc/nginx/nginx.conf \
  --network hmall \
  nginx
```

测试，通过浏览器访问：http://你的虚拟机ip:18080

### DockerCompose

大家可以看到，我们部署一个简单的java项目，其中包含3个容器：

- MySQL
- Nginx
- Java项目

而稍微复杂的项目，其中还会有各种各样的其它中间件，需要部署的东西远不止3个，比如redis、MQ等等。如果还像之前那样手动的逐一部署，就太麻烦了。

而Docker Compose就可以帮助我们实现**多个相互关联的Docker容器的快速部署**。它允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器。

#### 基本语法

docker-compose.yml文件的基本语法可以参考官方文档：

> [Compose file version 3 reference | Docker Docs](https://docs.docker.com/compose/compose-file/compose-file-v3/)

docker-compose文件中可以定义多个相互关联的应用容器，每一个应用容器被称为一个服务（service）。由于service就是在定义某个应用的运行时参数，因此与`docker run`参数非常相似。

举例来说，用docker run部署MySQL的命令如下：

```Bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  --network hmall
  mysql
```

如果用`docker-compose.yml`文件来定义，就是这样：

```YAML
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
    networks:
      - new
networks:
  new:
    name: hmall
```

对比如下：

| **docker run 参数** | **docker compose 指令** | **说明**   |
| :------------------ | :---------------------- | :--------- |
| --name              | container_name          | 容器名称   |
| -p                  | ports                   | 端口映射   |
| -e                  | environment             | 环境变量   |
| -v                  | volumes                 | 数据卷配置 |
| --network           | networks                | 网络       |

明白了其中的对应关系，相信编写`docker-compose`文件应该难不倒大家。

黑马商城部署文件：

```YAML
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on: # 不写也可以，写了显式声明，按顺序创建
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall # 真正网络的名字
```

#### 基础命令

编写好docker-compose.yml文件，就可以部署项目了。常见的命令：

https://docs.docker.com/compose/reference/

基本语法如下：

```Bash
docker compose [OPTIONS] [COMMAND]
```

其中，OPTIONS和COMMAND都是可选参数，比较常见的有：

| Options | **参数或指令** | **说明**                                                     |
| ------- | -------------- | ------------------------------------------------------------ |
|         | -f             | 指定compose文件的路径和名称 **如果文件就在当前目录，且名字就为docker-compose ，就无需加** |
|         | -p             | 指定project名称。project就是当前compose文件中设置的多个service的集合，是逻辑概念,**无需指定，默认root-容器名** |
|         | -d             | 后台运行                                                     |



| Commands | 参数或指令 | **说明**                     |
| -------- | :--------- | :--------------------------- |
|          | up         | 创建并启动所有service容器    |
|          | down       | 停止并移除所有容器、网络     |
|          | ps         | 列出所有启动的容器           |
|          | logs       | 查看指定容器的日志           |
|          | stop       | 停止容器                     |
|          | start      | 启动容器                     |
|          | restart    | 重启容器                     |
|          | top        | 查看运行的进程               |
|          | exec       | 在指定的运行中容器中执行命令 |

教学演示：

```Bash
# 1.进入root目录
cd /root

# 2.删除旧容器
docker rm -f $(docker ps -qa)

# 3.启动所有, -d 参数是后台启动
docker compose up -d
# 结果：
......

# 4.查看镜像
docker compose images
# 结果
CONTAINER           REPOSITORY          TAG                 IMAGE ID            SIZE
hmall               root-hmall          latest              32eebee16acd        362MB
mysql               mysql               latest              3218b38490ce        516MB
nginx               nginx               latest              605c77e624dd        141MB

# 5.查看容器
docker compose ps
# 结果
NAME                IMAGE               COMMAND                  SERVICE             CREATED             STATUS              PORTS
hmall               root-hmall          "java -jar /app.jar"     hmall               54 seconds ago      Up 52 seconds       0.0.0.0:8080->8080/tcp, :::8080->8080/tcp
mysql               mysql               "docker-entrypoint.s…"   mysql               54 seconds ago      Up 53 seconds       0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp
nginx               nginx               "/docker-entrypoint.…"   nginx               54 seconds ago      Up 52 seconds       80/tcp, 0.0.0.0:18080-18081->18080-18081/tcp, :::18080-18081->18080-18081/tcp
```

打开浏览器，访问：http://yourIp:8080

## 部署实战 -苍穹外卖

**redis** 

在虚拟机根目录下创建redis目录,其下新建conf目录，将配置文件放入

```shell
docker run -d 
	-v ./redis/conf:/usr/local/etc/redis
	--name redis #容器名
	-p 6379:6379 
	redis #镜像名
	redis-server /usr/local/etc/redis/redis.conf
```

```yml
  redis:
    image: redis
    container_name: redis
    ports:
      - "6379:6379"
    volumes:
      - "./redis/conf:/usr/local/etc/redis"
    command:
      - redis-server /usr/local/etc/redis/redis.conf
    networks:
      - balance-net
```

**mysql**

在虚拟机根目录下创建mysql目录，其下新建data init conf目录，将`sky_take_out.sql`文件放入init目录，将`sky.conf`文件放入conf目录，此文件为数据库字符集相关设置

```shell
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -v ./mysql/data:/var/lib/mysql \
  -v ./mysql/conf:/etc/mysql/conf.d \
  -v ./mysql/init:/docker-entrypoint-initdb.d \
  mysql
```

```yml
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    volumes:
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123456
    networks:
      - balance-net
```

**nginx**

在虚拟机根目录下创建nginx目录，将html文件和conf配置上传到其下

```shell
docker run -d
	--name nginx
	-p 80:80
	-v ./nginx/html:/usr/share/nginx/html
	-v ./nginx:/etc/nginx/nginx.conf
	nginx:1.20.2
```

```yml
  nginx:
    image: nginx:1.20.2
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    networks:
      - balance-net
```

**后端项目**

```yml
  cangqiongwaimai:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: cangqiongwaimai
```

**汇总：**

```yml
version: '1.0'

services:
  redis:
    image: redis
    container_name: redis
    ports:
      - "6379:6379"
    volumes:
      - "./redis/conf:/usr/local/etc/redis"
    command:
      - redis-server /usr/local/etc/redis/redis.conf
    networks:
      - balance-net
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    volumes:
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123456
    networks:
      - balance-net
  nginx:
    image: nginx:1.20.2
    container_name: nginx
    ports:
      - "80:80"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    networks:
      - balance-net
  cangqiongwaimai:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: cangqiongwaimai
    ports:
      - "8080:8080"
    networks:
      - balance-net
networks:
  balance-net:
    name: balance
```

现在我们已经将docker-compose文件编写完成，接下来，编写项目的dockerfile

```dockerfile
# 基础镜像
FROM openjdk:8
# 设定时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
# 拷贝jar包 注意xxx.jar为源文件路径 /app.jar为容器内路径
COPY xxx.jar /app.jar
# 入口
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

将 docker-compose  dockerfile sky-server-1.0-SNAPSHOT.jar上传至虚拟机根目录

```shell
# 启动并创建
docker compose -p cangqiongwaimai up -d

# 停止
docker compose stop
```

### 注意事项

其余的配置按部就班就可以，注意nginx配置文件

```config
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;
	
	map $http_upgrade $connection_upgrade{
		default upgrade;
		'' close;
	}

	upstream webservers{
	  server balance:8080 weight=90 ;
	}

    server {
        listen       80;
        # server_name  192.168.40.1;



        location / {
            root   /usr/share/nginx/html/sky;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
 
        # 反向代理,处理管理端发送的请求
        location /api/ {
			proxy_pass   http://balance:8080/admin/;
            #proxy_pass   http://webservers/admin/;
        }
		
		# 反向代理,处理用户端发送的请求
        location /user/ {
            proxy_pass   http://webservers/user/;
        }
		
		# WebSocket
		location /ws/ {
            proxy_pass   http://webservers/ws/;
			proxy_http_version 1.1;
			proxy_read_timeout 3600s;
			proxy_set_header Upgrade $http_upgrade;
			proxy_set_header Connection "$connection_upgrade";
        }


    }

}
```

尤其注意这一行

```
location / {
    root   /usr/share/nginx/html/sky;
    index  index.html index.htm;
}
```

需要填写容器内的地址，而不是挂载的真实地址

如果是windows,注意两者的区别

```
location / {
    root   html/sky;
    index  index.html index.htm;
}
```

