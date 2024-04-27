# 微服务

## 配置环境

### 安装mysql

首先将所给资料中的mysql目录(内含配置文件和初始化脚本)上传到根目录下，即

```
/root
```

然后创建一个通用网络：

```Bash
docker network create hm-net
```

使用下面的命令来安装MySQL：

```Bash
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -v /root/mysql/data:/var/lib/mysql \
  -v /root/mysql/conf:/etc/mysql/conf.d \
  -v /root/mysql/init:/docker-entrypoint-initdb.d \
  --network hm-net\
  mysql
```

此时，通过命令查看mysql容器是否正常运行：

```Bash
docker ps
```

此时，如果我们使用navicat连接MySQL，应该能发现已经创建了黑马商城所需要的表：

因为是虚拟机连接，可能会出现拒绝连接的情况，此时使用命令

```shell
docker exec -it mysql bash
mysql -u root -p
grant all privileges on *.* to 'root'@'%' with grant option;
```

即可让所有机器都可以连接

### 配置后端项目

在课前资料提供了一个hmall目录，用idea打开

此时在 hm-service中有两个配置文件,修改一下

`dev`

```yml
hm:
  db:
    host: mysql
    pw: 123456
```

`local`

```yml
hm:
  db:
    host: mysql
    pw: 123456
```

此时 `application.yml`文件中启动的是dev，我们需要修改成local，因为还未使用docker部署。

此处采用不修改的方式，而是在idea中配置启动项，从而指定配置文件

![image-20240427211407222](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240427211407222.png)

注意此处是jdk11，记得也要修改。

启动测试一下

`http://localhost:8080/hi`

得到返回

```
欢迎访问黑马商城, 这是您第1次访问
```

### 配置nginx

在课前资料中还提供了一个hmall-nginx的目录：

其中就是一个nginx程序以及我们的前端代码，直接在windows下将其复制到一个非中文、不包含特殊字符的目录下。然后进入hmall-nginx后，利用cmd启动即可：

```cmd
# 启动nginx
start nginx.exe
# 停止
nginx.exe -s stop
# 重新加载配置
nginx.exe -s reload
# 重启
nginx.exe -s restart
```

>特别注意：
>
>nginx.exe 不要双击启动，而是打开cmd窗口，通过命令行启动。停止的时候也一样要是用命令停止。如果启动失败不要重复启动，而是查看logs目录中的error.log日志，查看是否是端口冲突。如果是端口冲突则自行修改端口解决。
>
>双击启动不便于关闭

启动成功后，访问http://localhost:18080，应该就能看到我们的门户页面