# Linux

一切皆文件

## 切换输入法

`win+space`

## 网络连接三种模式

### 桥接模式

虚拟系统和外部系统相互通讯，但容易造成IP冲突，因为需要一个同一网段的ip，但ip数没那么多。网速快

### NAT模式

 指定一个虚拟机内网ip（不要求同网段），主机指定一个ip与其相互通讯，虚拟机与外部主机通讯借助主机的ip

### 主机模式

独立系统，不和外部发生联系

## 虚拟机克隆

### 方式一 直接拷贝

直接拷贝一份安装好的虚拟机文件，然后用vmware打开即可

### 方式二 vmware克隆

**注意，克隆时需要先关闭Linux系统**

## 虚拟机快照  存档读档

![vmphoto](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202309211552683.PNG)

## Linux目录

|     目录名称     | 含义                                                         |
| :--------------: | :----------------------------------------------------------- |
|       `/`        | 根目录，其他目录都在其之下                                   |
|      `/afs`      | 通常用于存储与 Andrew File System 文件系统（AFS）相关的数据和挂载点。AFS是一个**分布式网络文件系统**，旨在提供可扩展的、安全的文件存储和共享解决方案 |
|     `/boot`      | 存放启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件 |
|      `/etc`      | **所有系统及应用所需要的配置文件**                           |
|      `/lib`      | 系统开机所需要最基本的动态连接共享库，其作用类似于windows的DLL文件，几乎所有的应用程序都需要用到这些共享库 |
|     `/media`     | linux系统会自动识别一些设备，例如U盘、光驱等，当识别后，Linux会把识别的设备挂载到这个目录下 |
|      `/opt`      | optional 通常用于**存放大型应用程序或第三方应用程序包**      |
|     `/root`      | 包含了系统管理员的个人长度文件和配置信息                     |
|     `/sbin`      | s是super User的意思，存放系统管理员使用的系统指令            |
|      `/sys`      | 用于提供对内核和系统硬件的运行时信息和控制接口。这个目录中包含了许多虚拟文件和目录，允许用户和系统进程与内核进行交互以获取有关系统配置、硬件设备和内核参数的信息。 |
|      `/usr`      | 用户很多的应用程序和文件都放在此目录下。包含了用户可执行程序、库文件、头文件、文档、图标等系统软件和用户程序所需的文件。 |
|      `/bin`      | Binary的缩写，存放常用的基本指令                             |
|      `/dev`      | device的简写，类似Windows的设备管理器，把所有的硬件用文件形式存储 |
|     `/home`      | 存放普通用户的主目录，每个普通用户都有自己的一个目录，一般以用户名命名 |
|     `/lib64`     | 通常用于存放64位系统架构的共享库文件。这个目录与 `/lib` 目录不同，它**专门用于存储64位系统所需的共享库** |
|      `/mnt`      | 系统提供该目录是为了让用户临时挂载别的文件系统，我们可以将外部存储挂载在`/mnt`下，然后进入该目录就可以查看里面的内容了。 |
|     `/proc`      | 虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息   |
|      `/run`      | **临时文件系统**（tmpfs）目录，**用于存储在系统启动期间和系统运行时产生的临时文件**、套接字文件、进程ID文件（PID文件）等 |
|      `/srv`      | service的缩写，存储本地服务器（服务）的相关数据文件。这个目录通常包含了各种网站、文件共享、FTP服务器等服务器软件所需的数据和资源。 |
|      `/tmp`      | 存储临时文件和临时数据。**通常用于存储用户和应用程序生成的临时文件** |
|      `/var`      | 用于存储在运行过程中会变化的数据、文件和日志信息。           |
|  `/lost+found`   | 存放系统非法关机后的文件，一般情况下为空                     |
|                  |                                                              |
|    `/usr/bin`    | 后期安装的软件的运行脚本(可执行文件)                         |
| **`/usr/local`** | **存放用户或系统管理员自行安装的本地软件和应用程序,适合用户自行安装的小型软件** |
|     /seLinux     | security-enhanced  linux,安全子系统，能控制程序只能访问特定文件，有三种工作模式 |

##  文件后缀

| 后缀名 | 含义                                                         |
| :----: | :----------------------------------------------------------- |
| `.cfg` | 通常是配置文件的扩展名，用于存储应用程序、服务或系统的配置参数和设置。这些文件包含了各种配置选项，用户或管理员可以根据需要进行修改，以自定义应用程序或系统的行为。一般为`.cfg`,有的为`.conf`,比如Apache HTTP Server 配置文件。 |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |
|        |                                                              |



## 常用指令

### 系统相关

#### reboot 重启


在Linux系统中，`reboot`指令用于重新启动计算机。它是一个非常基本且强大的命令，通常需要超级用户（root）权限才能执行，因为重新启动计算机可能会中断正在进行的操作，对系统和数据安全造成潜在风险。以下是有关`reboot`指令的详细解释：

```bash
reboot [选项]
```

选项可以是以下之一：

1. `-p` 或 `--poweroff`：这个选项会将系统关机而不是重新启动。使用此选项时，系统将被彻底关闭，而不是重新启动。
2. `-f` 或 `--force`：强制重启，即使有未保存的数据或进程仍在运行。这可能导致数据损失或文件系统损坏，应该谨慎使用。
3. `-h` 或 `--halt`：这个选项会使系统停止运行，类似于关机。系统将停在一个停机的状态，而不会重新启动。
4. `-i` 或 `--init`：通过向init进程发送指定信号（默认为SIGTERM）来重启系统。这是正常的重启方式，会让系统正常地关闭并重新启动。
5. `-w` 或 `--wtmp-only`：只更新`/var/log/wtmp`文件中的系统重启记录，而不进行实际的重启操作。这对于手动记录重启时间很有用。
6. `-d` 或 `--debug`：在重启期间输出调试信息，用于故障排除。

要执行普通的系统重启，只需在终端中输入`reboot`即可，然后系统将开始关闭并重新启动。请注意，使用`reboot`命令前请确保保存了所有重要的工作和文件，以避免数据丢失。

#### shutdown 关机

`shutdown` 命令是 Linux 操作系统中用于关闭或重新启动计算机的命令。它提供了多种选项，可以让你以不同的方式关闭或重新启动系统。以下是 `shutdown` 命令的详细说明：

基本语法：

```sh
shutdown [选项] 时间 [消息]
```

- `选项`：指定关闭或重新启动的方式和其他选项。常用的选项包括：
  - `-r` 或 `--reboot`：重新启动系统。
  - `-h` 或 `--halt`：关闭系统。
  - `-c`：取消当前计划的关机或重启操作。
  - `-k`：发送警告消息，但不执行关机或重启操作（模拟操作）。
  - `-t`：指定延迟时间，即计划的关机或重启时间。
- `时间`：指定关机或重启的时间，可以有以下格式：
  - `now`：立即执行关机或重启。
  - `+分钟` 或 `hh:mm`：延迟指定的分钟数或指定的时刻执行操作。
- `消息`（可选）：可以提供一条关于关机或重启的消息，这条消息会广播给登录系统的用户。

示例用法：

1. 立即关闭系统：

   ```sh
   sudo shutdown -h now
   ```

2. 立即重启系统：

   ```sh
   sudo shutdown -r now
   ```

3. 在5分钟后关闭系统并发送关机消息：

   ```sh
   sudo shutdown -h +5 "系统将在5分钟后关闭，请保存工作。"
   ```

4. 取消计划的关机或重启：

   ```sh
   sudo shutdown -c
   ```

5. 模拟关机或重启操作并发送警告消息：

   ```sh
   sudo shutdown -k +2 "系统将在2分钟后关闭，请注意。"
   ```

注意事项：

- 执行 `shutdown` 命令需要管理员或超级用户权限，通常需要使用 `sudo`。
- 关机或重启操作可能需要一些时间，因此提前通知用户是个好习惯。
- 在执行关机或重启前，请确保保存了所有未保存的工作，以避免数据丢失。
- 取消关机或重启操作可以使用 `-c` 选项。
- 在执行关机或重启前，通常会有一个倒计时或警告消息，以便用户有足够的时间保存工作并退出登录。

#### halt 简单关机

`halt` 是一个Linux系统命令，用于关闭计算机并将其停止。它将系统关闭到一个无操作的状态，不会重新启动系统，也不会执行任何进一步的操作。`halt` 命令的用法非常简单：

```sh
halt
```

或者

```sh
poweroff
```

这两个命令的效果相同，都会关闭系统并将其停止。当你运行 `halt` 命令时，系统会执行以下操作：

1. 关闭所有运行中的进程。
2. 卸载所有文件系统。
3. 关闭硬件设备。
4. 最终将系统停止，不再运行。

需要注意的是，`halt` 命令需要具有管理员权限的用户才能运行，通常需要使用 `sudo` 来执行。如果你没有管理员权限，你可能需要使用 `sudo` 或联系系统管理员来执行此操作。

在使用 `halt` 命令之前，请确保保存了所有未保存的工作，并确保系统已备份或没有重要任务运行，因为它将无条件地关闭系统，不会给用户提示或提供任何警告。

这与 `shutdown` 命令不同，`shutdown` 命令可以根据选项来控制系统的关闭行为，例如发送警告消息、延迟关闭时间或重新启动系统。而 `halt` 命令只是简单地关闭系统并停止它。

#### sync 文件同步到磁盘

`sync` 命令是用于 Linux 系统的基本命令，它用于将内存中的文件系统数据同步到磁盘中。在 Linux 中，文件系统通常在内存中缓存，以提高文件访问速度，但为了确保数据的持久性和一致性，需要定期将内存中的数据写入磁盘。`sync` 命令允许你手动执行这个同步操作，以确保数据的安全性。以下是 `sync` 命令的详细说明：

基本语法：

```sh
sync
```

`sync` 命令不需要任何参数或选项。它只是触发将内存中的文件系统数据写入磁盘的操作。

使用示例：

```sh
sync
```

`sync` 命令的主要作用是：

1. 将文件系统缓存中的数据强制写入磁盘，以确保数据的持久性。这对于避免数据丢失非常重要，特别是在系统关机之前。
2. 帮助防止磁盘上的数据损坏。通过在关机前使用 `sync` 命令，可以确保文件系统的元数据和数据得到正确的同步，降低磁盘损坏的风险。

需要注意的是，通常情况下，Linux 系统会定期自动执行数据同步操作，因此不需要经常手动运行 `sync` 命令。但在某些情况下，如系统关机之前，特别是在非正常关机或电源故障的情况下，手动运行 `sync` 命令是一个很好的实践，以确保数据的完整性和一致性。

请注意，`sync` 命令不提供任何输出或反馈，它只是在后台执行同步操作。当你运行 `sync` 命令后，可以安全地关机或重新启动系统，以确保文件系统的数据得到正确的写入磁盘。

#### su 提权

`su` 和 `su - root` 是两种不同的方式来切换到超级用户（root）账户的命令，它们之间有一些重要的区别。

1. **`su` 命令**：
   - `su` 表示 "switch user"，用于切换到另一个用户的身份，通常是超级用户（root）。
   - 当你运行 `su` 命令时，系统会要求你输入超级用户（root）的密码，以验证你的身份。
   - `su` 命令会切换到超级用户的身份，但不会改变当前工作目录（即不改变当前环境变量和路径）。
   - 环境变量（如PATH）和其他用户设置都保持不变，只是用户身份变成了root。这意味着你仍然在之前的用户环境中工作，可能会受到用户环境设置的影响。
   - 例如，如果你运行 `su` 命令后执行某个命令，该命令将在root用户的环境中运行，但环境变量可能仍然是原用户的环境变量。
2. **`su - root` 命令**：
   - `su - root` 也是用于切换到root用户的身份，但它的行为略有不同。
   - 与 `su` 不同，`su - root` 会切换到root用户的身份，并同时切换到root用户的环境。
   - 这意味着它会加载root用户的环境变量、工作目录以及其他设置，使你处于一个全新的root用户环境中，就像你刚登录到root账户一样。
   - 使用 `su - root` 可以确保你在一个干净的root环境中工作，避免了可能由于原用户环境设置引起的问题。

总结来说，主要的区别在于环境变量和工作目录的切换。`su` 只切换用户身份，但保持当前环境，而 `su -`（或 `su - root`）会同时切换到新用户的环境，提供更干净的工作环境。通常情况下，如果你需要执行需要root权限的任务，建议使用 `su - root`，以确保你在一个干净的root环境中工作。

注意使用`su - 用户名`可以切换指定用户，权限高到权限低不需要密码，反之需要

#### logout exit 注销用户

在 Linux 终端中，要注销当前用户会话，你可以使用以下几种方法之一：

1. **使用 `exit` 命令**：

   最简单的方法是在终端中输入 `exit` 命令，然后按下回车键。这会结束当前用户的会话并注销该用户。

   ```
   exit
   ```

2. **使用 `logout` 命令**：

   另一种方法是使用 `logout` 命令，它也会结束当前用户的会话并注销该用户。`logout` 命令与 `exit` 作用相同。

   ```
   logout
   ```

3. **使用 Ctrl + D 键盘快捷键**：

   你还可以使用键盘快捷键 `Ctrl + D` 来注销当前用户。在终端中按下 `Ctrl + D` 组合键将执行与 `exit` 或 `logout` 相同的操作。

   ```
   Ctrl + D
   ```

这些方法中的任何一个都可以用来注销当前用户的会话，**并返回到登录界面或切换到另一个用户**。注销用户会话是确保系统安全性和用户隐私的重要步骤。请确保在注销前保存了未保存的工作，以防止数据丢失。

#### ps 进程

`ps` 是一个用于显示当前系统中正在运行的进程信息的命令。其名称来自 "process status"。通过 `ps` 命令，你可以获取有关进程的详细信息，包括进程的 ID、状态、占用的资源等。

常见的 `ps` 命令选项包括：

- `-e`：显示所有进程。
- `-f`：显示详细的进程信息。
- `-aux`：以详细和用户友好的方式显示所有进程。
- `-l`：以长格式显示进程信息。

例如，常见的使用方式如下：

```shell
ps -ef # 以全格式显示当前所有进程 有父进程

root 1279 1 0 2023 ? 00:00:00 /usr/sbin/atd -f：

root: 进程所有者是 root 用户。
1279: 进程的进程标识符（PID）为 1279。
1: 进程的父进程标识符（PPID），即创建该进程的父进程的PID为1，通常是 init 进程。
0: 进程的优先级。
2023: 进程的启动时间或日期。
?: 进程的终端信息，因为它是守护进程，没有关联的终端。
00:00:00: 进程的累计运行时间。
/usr/sbin/atd -f: 进程的命令行，表示该进程是 /usr/sbin/atd 程序，使用 -f 选项启动。
```

这将显示所有进程的详细信息，包括进程的 PID（进程标识符）、状态、占用的资源等。

#### free 内存使用情况

例如，要以人类可读的格式显示内存使用情况，可以使用以下命令：

```shell
free -h
```

#### lscpu 查看cpu

```shell
lscpu
```

#### df  查看磁盘

`df`命令用于显示文件系统的磁盘使用情况。常用的选项是`-h`（或`--human-readable`），它可以以人类可读的格式（例如KB、MB、GB）显示磁盘使用情况。

```shell
df -h
```

### 网络

#### ip 地址

在较新的 Debian 系统中，`ifconfig` 命令可能已经被弃用，而使用 `ip` 命令作为替代。你可以尝试使用以下命令查看网络接口信息：

```shell
ip a
```

上述命令将显示当前系统上的所有网络接口及其详细信息。

### ss/netstat 查看端口

1. **netstat**：这是一个非常强大的命令，可以显示详细的网络状态信息，包括网络连接、路由表、接口状态等。如果要查看所有监听的端口，可以使用`netstat -l`命令。如果只关心TCP端口，可以使用`netstat -lt`，对于UDP端口，可以使用`netstat -lu`。
2. **ss**：这是一个用来查看系统套接字信息的工具，可以看作是`netstat`的替代品。如果要查看所有监听的端口，可以使用`ss -l`命令。

### 用户

#### id 

id+用户名，查看其基本信息

```shell
id balance
```

#### adduser 添加用户

```
adduser 用户名
```

注意如果 `adduser` 已经是最新版本，但依然无法执行 `adduser` 命令。**可能是由于 `$PATH` 环境变量中没有包含 `adduser` 的路径。**

在大多数系统中，`adduser` 通常位于 `/usr/sbin` 目录下。你可以使用以下命令：

```shell
sudo /usr/sbin/adduser jack
```

如果这样能够成功执行 `adduser` 命令，说明 `adduser` 程序的路径没有被正确地包含在你的 `$PATH` 中。

你也可以查看 `$PATH` 变量，看一下是否包含了 `/usr/sbin` 目录。可以使用以下命令查看：

```shell
echo $PATH
```

#### passwd 修改密码

```
passwd 用户名
```

#### userdel 删除用户

删除用户，保留home里的目录

```
userdel 用户名 
```

删除用户及home里的目录

```
userdel -r 用户名
```

#### who am i 查看当前用户

```
who am i
```

### 文件目录指令

#### ls

列出当前目录下文件

注意，linux中隐藏文件以 `.`开头，但普通ls不会列出隐藏文件

```bash
ls -a #列出所有文件，包括隐藏文件
ls -l #按行显示

ls -la

ls可以加文件名，会显示文件相关信息
ls -l hello.txt
```

这些后缀参数都可以一起使用无所谓先后

#### pwd 显示目录

显示当前目录 print work directory

#### cd 切换目录

`cd ~`:回到用户目录

`cd -`:根目录

`cd ..`上一级目录

#### mkdir 创建目录

创建一个目录 `mkdir 目录名`

注意有时候你或许这样使用 `mkdir hello.txt`实际上创建出来的不是txt，而是目录名为hello.txt的目录

![image-20240108183134253](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401081831597.png)

创建多级目录`mkdir -p 目录名/目录名....`

`-p` 是一个选项，表示如果中间级目录不存在，则会自动创建

也可以指定创建的位置`mkdir -p hello/hhh/hh`,这将在hello下创建hhh，在hhh下创建hh目录

**当你执行 `mkdir hello/hhh/hh` 这样的命令时，系统会按照路径的层次结构逐级创建目录，如果其中某个目录不存在的话。**

#### rmdir 删除空目录

`rmdir 目录名`

`rm -rf` 删除非空目录

#### touch 创建空文件

```
touch 文件名
```

#### cp 拷贝

拷贝文件到指定目录

```
cp [选项] source dest

-r :递归复制整个文件夹
source:源文件
dest：目标目录

cp hello.txt hello2.txt # 也可只复制文件
```

![image-20240108184831438](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401081848347.png)

强制覆盖所有文件不提示`cp -r xxx  xxx`

#### rm 删除

删除一个文件/目录：`rm /home/hello.txt`
删除整个文件夹：`rm -rf /home/hello.txt`

```
-r 递归删除整个文件夹
-f 强制删除不提示
```

#### mv 移动文件/目录 重命名

重命名：`mv oldNameFile newNameFile`

**注意重命名也可以更改文件后缀**

移动文件/目录：`mv 文件 新目录`

**注意此时若指定新目录下文件名称，也会执行重命名功能**

#### cat 查看文件

`cat 文件名`

```
cat -n file.txt
显示 file.txt 文件的内容，并在每一行前面加上行号。
```

若要带上行号`cat -n 文件名`，可以和管道命名 more 合用

管道命令：前面命令的结果作为管道命令的输入 `cat -n 文件名 | more`

**按“Enter”表示显示下一行，“空格”表示翻一页**

#### more 查看文件

more即可和cat等指令一起使用，也可以单独使用，但more无选项显示行号

`more 文件名`

![image-20240108191406398](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401081914794.png)

#### less 查看大文件较好

less指令在显示文件内容时，并不是一次将整个文件加载之后再显示，而是根据显示需要加载内容

![image-20240109184919271](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401091849672.png)

#### head 显示文件的开头部分

默认情况下head指令显示文件的前10行内容

```shell
head 文件	#查看文件头10行内容
head -n 5 文件 #查看文件头5行内容，5可以是任意行数
```

#### tail 显示文件尾部内容 重要

默认情况下tail指令显示文件的后10行内容

```shell
tail 文件	#查看文件后10行内容
tail -n 5 文件	#查看文件后5行内容，5可以是任意行数
```

`tail -f 文件`**实时追踪该文档的所有更新，工作经常使用**

#### **echo 输入内容到控制台**

```shell
echo [选项] [输出内容]
echo $PATH 
echo $HOSTNAME
```

写shell脚本时往往会用到 echo 进行输出比如 echo "hello world"

#### `>`(输出重定向)和`>>`(追加)指令

举个例子，一般输出

```shell
echo $PATH 
```

会输出显示到控制台，而

```shell
echo $PATH > hello.txt
```

现在会将$PATH写入hello.txt且为**覆盖写**

`>>`这个是追加写入

**常见用法**

```shell
ls -l > 文件	#列表的内容写入文件a.txt中(覆盖写)
ls -al >> 文件	#列表的内容追加到文件aa.txt的末尾
cat 文件1 > 文件2	#将文件1的内容覆盖到文件2
echo "内容" >> "文件"
```

注意若目标文件不存在会创建

#### ln 软连接

软链接也叫符号链接，类似于windows里的快捷方式，主要存放了链接其他方式的路径

```shell
ln -s [文件或目录] [软链接名] #功能描述: 给文件创建一个软链接
# -s 代表软链接

ln file1.txt file1_link.txt
#创建 file1.txt 的硬链接，命名为 file1_link.txt。
```

当我们使用pwd指令查看目录时，仍然看到的是软链接所在目录

- **硬链接**：硬链接是指向源文件数据的另一个目录条目。硬链接文件和源文件共享相同的数据块。因此，它们有相同的文件内容和文件 inode。删除源文件不会删除硬链接文件，因为文件的数据块仍被硬链接引用。
- **符号链接**：符号链接是指向源文件或目录路径的一个文件。符号链接与源文件没有共享数据块，它只是一个指向源文件或目录的路径。删除源文件会使符号链接变成无效的，因为它不再指向有效的路径。

### 时间日期指令

#### date 日期

```shell
date	#显示当前时间
date+%Y	#显示当前年份
date+%m	#显示当前月份
date+%d	#显示当前是哪一天
date "+%Y-%m-%d %H:%M:%S"	#显示年月日时分秒

date -s 2020-10-09 11:22:22 #设置时间为2020-10-09 11:22:22
```

#### cal 日历信息

```shell
cal [选项] 	#不加选项，显示本月日历
```

### 查找指令

#### find

将从指定目录向下递归地遍历其各个子目录，将满足条件的文件或者目录显示在终端

```shell
find [搜索范围/指定的目录] [选项]
-name <查询方式> #在指定路径下按照文件名查找文件  find /home -name hello.txt
-user <用户名> #查找属于指定用户名所有文件   find /home -user balance
-size <文件大小> #按照指定文件大小查找文件   find /home -size +10M 大于10M -10M 小于 10M 等于

find . -name "host*" # 当前目录下按名模糊查询

find . -name ".bash*" # 当前目录下查询隐藏文件bash开头的文件， . 表示一个隐藏文件
```

#### locate 定位文件路径

locate指令利用事先建立的系统中所有文件名称及路径的locate数据库实现快速定位给定的文件。

**Locate指令无需遍历整个文件系统，查询速度较快**。为了保证查询结果的准确度，管理员必须定期更新locate

由于locate指令基于数据库进行查询，所以**第一次运行前，必须使用updatedb指令创建locate数据库**

```shell
locate filename # 将在文件数据库中查找包含指定名称的文件
locate -b '\directoryname' # 查找包含指定名称的目录。
locate -n 5 filename #限制搜索结果的数量，仅显示前 5 条匹配项。
locate -i filename #忽略文件名的大小写。
locate -r 'filename' #显示文件的完整路径。
locate -e '/excluded_path' filename #在搜索中排除指定的路径。
```

使用 updatedb 命令手动更新数据库

```shell
sudo updatedb # 使用 updatedb 命令手动更新数据库。
```

#### grep 文本文件过滤搜索

> Global Regular Expression Print

grep过滤查找，管道符，“|”，表示将前一个命令的处理结果输出传递给后面的命令处理

```shell
grep [选项] 模式 文件
[选项] 是可选的命令行选项，用于指定搜索的行为。 -i 忽略大小写 -n显示行号 --color 高亮显示
模式 是一个正则表达式模式，用于定义搜索的文本模式。
文件 是要搜索的文件名。
# 示例 此命令只会显示行内有hello的行
cat hello.txt | grep "hello" 

grep -o 'example' file.txt | wc -l #一个文档关键字出现的总次数
```

### 压缩和解压相关指令

#### gzip/gunzip

gzip：用于压缩文件，gunzip用于解压文件

**用于压缩单个文件，不直接支持压缩多个文件或目录。**

```shell
gzip 文件	#压缩文件，只能将文件压缩为*.gz文件
gunzip 文件.gz	#解压缩文件命令

# 示例：
gzip /home/hello.txt
gunzip /home/hello.txt.gz
```

#### zip/unzip

zip用于压缩文件，unzip用于解压

**支持多文件压缩**

```shell
zip [选项] XXX.zip 将要压缩的内容	#压缩文件和目录的命令
unzip [选项] XXX.zip	#解压缩文件
/*
	zip 常用选项
		-r 递归压缩，即会压缩目录
	unzip 常用选项
		-d 目录	指定解压后文件的存放目录
*/
#示例 
zip -r myhome.zip /home/ #将home目录及其子目录/文件都压缩
uzip -d /opt/tmp  myhome.zip #将myhome.zip解压到/opt/tmp文件下
```

#### tar 打包

`tar` 本身并不进行压缩(指定选项也可进行压缩)，它只是将文件和目录组合成一个单一的归档文件。

```shell
tar [选项] XXX.tar.gz 打包的内容 #XXX.tar.gz 是你指定的文件名
常用选项
-c    产生.tar打包文件
-v    显示详细信息
-f    指定文件名
-z    -z是代表--gzip,--ungzip，因为文件后缀有gz，所以不管压缩还是解压都要加
-x    解压tar文件
# 示例
tar -zvcf hello.tar.gz /home/hello.txt /home/hello2.txt 
tar -zxvf hello.tar.gz # 当前目录解压
tar -zxvf hello.tar.gz -C /opt/tmp # /opt/tmp目录下解压 注意-C，需大写
```

### 其他

#### clear

清除当前终端内容

#### man

```
man 指令名
```

显示指令的帮助文档（中文）

#### help

```bash
help 指令名
```

显示指令的帮助文档（英文）

#### which

用于在 Linux 或类 Unix 系统上查找可执行文件路径的命令。它用于确定给定命令的实际路径。

```shell
which 命令
```

#### history

查看已经执行过历史命令，也可以执行历史指令

```shell
history		# 查看已经执行过历史命令
history 10 #显示最后执行的 10 条命令
!n    #这里的n是命令的编号，通过history命令查看。例如，!100 将重新执行历史记录中编号为 100 的命令
!! #重新执行刚刚输入的命令。
history -c #清除当前 shell 会话的历史记录。
```

## vi 和 vim

### vim

![image-20231008184645192](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310081846737.png)

![image-20231009164008063](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202310091640997.png)

#### 正常模式快捷键

##### 拷贝当前行，或多行

```shell
yy
```

拷贝当前行 

```shell
数字+yy 如5yy
```

拷贝包括当前行向下的5行

##### 粘贴

```sh
p
```

##### 删除当前行，或多行

```shell
dd
```

删除当前行

```sh
数字+dd 如5dd
```

删除包括当前行向下的5行

##### 光标移至首行或尾行

```sh
G
```

移至首行

```sh
gg
```

移至尾行

##### 光标移至指定行

```sh
行数+shift+g
```

##### 撤销

```sh
u
```

#### 命令模式快捷键

##### 查找单词

```sh
/+查找的单词+回车 按n转到下一个
如/hello
```

查找完单词后一直高亮显示，可以使用如下命令取消高亮

```sh
:set nohlsearch  //或者:set noh 或者直接:noh
```

##### 设置文件行号

```sh
:+set nu  //设置行号
:+set nonu //取消行号
```

## 组

### 用户---组

linux中所有用户必须属于一个组，不能独立于组外。

新增组

```
groupadd 用户组名
```

删除组

```
groupdel 用户组名
```

增加用户时直接加上组

```shell
useradd -g 用户组 用户名 # 创建用户并将其放入给定组中
```

修改用户的组

```shell
usermod -g 用户组 用户名 # 更改用户所在组 -g代表是对其主组进行操作
```

用户组相关配置文件

*/etc/passwd：*用户配置文件，记录用户各种信息

*/etc/shadow：*口令（密码）配置文件

*/etc/group：*组的配置文件，记录Linux包含的组的信息

### 文件---组

在linux中每个文件有所有者，所在组，其他组的概念。

#### 所有者

谁创建文件，就为该文件的所有者

```shell
ls -alh # 查看文件所有者 -h 显示大小

total 16K # 该目录下所有文件大小之和
drwxr-xr-x. 4 root    root    4.0K Aug 3 15:34 .
drwxr-xr-x：所有者（1-3）-所在组成员（4-6）-其他组（7-9） 的权限 
# 第0位确定文件类型，d：目录 l：链接 c：字符设备 如鼠标，键盘 b：块设备 比如硬盘 -：普通文件
# r：read  可以读取查看
# w：write 可以修改但不能删除，只有对该文件所在目录有写的权限才可以修改该文件
# x：execute 可执行 可以被执行

4: 链接数，表示有多少文件链接到此处 若为目录表示有多少子目录
第一个root： 所有者
第二个root： 所在组
4.0k： 文件大小 如果是文件夹，显示4k
Aug 3 15:34: 最后修改的日期和时间
. ： 文件/目录名， .开头代表这是一个隐藏目录
```


```shell
chown 用户名 文件名/目录 # 更改文件所有者
chown 用户名:所在组 文件/目录 # 更改文件/目录所有者和所在组
chown -R 用户名:所在组 目录 # 更改目录及子目录子文件所有者和所在组
```

#### 所在组 

文件会归属于一个组（默认为所有者所在组），这个组中的用户对该文件有一些权限

change group"，其中 "ch" 表示 change，"grp" 表示 group

```shell
chgrp 组名 文件名 # 修改文件所在的组 -R 目录下所有文件/目录全部修改
```

####  其他组

对于所有者及所在者所在用户组以外的其他用户组，称为其他组

```shell
usermod -d 目录名 用户名 # 改变用户登录的初始目录
```

### 修改权限

通过chmod指令，可以修改文件或者目录的权限

#### 法一 + - =变更权限

```shell
# u:所有者 g:所在组 o:其他组 a:所有人
chmod u=rwx,g=rx,o=x 文件名/目录名 # 给该文件/目录所有者读写执行，所在组读执行，其他组执行权限
chmod o+w 文件名/目录名 # 给其他组赋予写的权限
chmod a-x 文件名/目录名 # 所有人剥夺执行权限
```

#### 法二 数字

```shell
# r=4 w=2 x=1 rwx=4+2+1=7
chmod u=rwx,g=rx,o=x xxx 等价于 chmod 751 xxx
```

### 文件夹的rwx

x: 表示可以进入到该目录，比如cd  **开门**

r: 表示可以ls，将目录的内容显示  **开灯**

w: 表示可以在该目录，删除或者创建文件 

## 任务调度crond

在Linux中，"crond" 是 "cron daemon" 的缩写。 "cron" 是一种用于在预定时间执行任务的时间管理工具，而 "daemon" 是在后台运行的系统进程。因此，"crond" 实际上是负责管理和执行 cron 作业的守护进程。

### **反复执行crontab** 

进行定时任务的设置

**任务调度**：是指系统在某个时间执行的特定的命令或程序

任务调度分类：

1. 系统工作：有些重要的工作必须周而复始地执行，如病毒扫描等
2. 个别用户工作：个别用户可能希望执行某些程序，比如对mysql数据库的备份

```shell
crontab [选项]
/*
	常用选项
		-e 编辑crontab定时任务
		-l 查询crontab任务
		-r 删除当前用户所有的crontab任务
*/

conrtab -r	\\终止任务调度
crontab -l	\\列出当前有哪些任务调度
service crond restart [重启任务调度]

# 每分钟执行一次 ls -l /etc/ > /tmp/to.txt
crontab -e
*/1 * * * * ls -l /etc/ > /tmp/to.txt
# ls -l /etc/ 和 ls -l /etc 等价，末尾斜杠只是表示它为目录，规范一点
```

![image-20240124133946888](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401241339011.png)

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| *        | 代表任何时间，比如第一个为*，一小时中每分钟都执行一次        |
| ,        | 代表不连续时间，比如 0 8,12,16 * * *表示每天的8点0分，12点0分，16点0分执行 |
| -        | 代表连续时间范围，比如 0 5 * * 1-6 周一到周六5点0分执行      |
| */n      | 表示每隔多久执行，*/10 * * * *，每隔10分钟执行一次           |

### 定时执行一次 at

at命令是一次性定时计划任务，**执行完一个任务后不再执行此任务**。

at的守护进程***atd***会以后台模式运行，检查作业队列来运行。默认情况下，atd守护进程每60秒检查作业队列，有作业时，会检查作业运行时间，如果时间与当前时间匹配，则运行此作业。

在**使用at命令的时候，一定要保证atd进程的启动**，可以使用指令来查看：`ps -ef | grep atd`

```shell
ps -ef: 这部分是一个用于显示当前系统中所有进程的命令。具体来说，-e 选项表示显示所有进程，而 -f 选项表示显示详细的进程信息。
```

命令格式：`at [选项] 时间`，最后键入两次**ctrl+d**结束命令的输入

- at时间定义
  - 接受在当天的hh:mm（小时:分钟）式的时间指定。假如该时间已过去，那么就放在第二天执行。例如：04:00
  - 使用midnight（深夜），noon（中午），teatime（饮茶时间，一般是下午4点）等比较模糊的词语来指定时间。
  - 采用12小时计时制，即在时间后面加上AM（上午）或PM（下午）来说明是上午还是下午。例如：12pm
  - 指定命令执行的具体日期。例如：04:00 2021-03-1
  - 使用相对计时法。指定格式为： **now + count 时间单位** ，时间单位能够是minutes、hours、days、weeks。例如：now + 5 minutes
- 案例
  - 2天后的下午5点执行/bin/ls /home
    `at 5pm + 2 days`
    `/bin/ls /home`
  - 使用**atq命令来查看系统中没有执行的工作任务**：`atq`
  - 明天17点钟，输出时间到/root/date100.log
    `at 5pm tomorrow`
    `date > /root/date100.log`
  - 删除已经设置的任务1：`atrm 1`

## Linux分区

> Linux来说无论有几个分区，分给哪一目录使用，它归根结底就只有一个根目录，一个独立且唯一的文件结构，Linux中每个分区都是用来组成整个文件系统的一部分。

Linux采用了一种叫“载入”的处理方法，它的整个文件系统中包含了一整套的文件和目录，且将一个分区和一个目录联系起来，这时要载入的一个分区将使它的存储空间在一个目录下获得。

![image-20240415104046667](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240415104046667.png)

Linux硬盘分IDE硬盘和SCSI硬盘，目前基本上是SCSI硬盘

- 对于IDE硬盘，驱动器标识符为“hdx~”，其中“hd”表明分区所在设备的类型，这里是指IDE硬盘了.
- "×”为盘号（a为基本盘，b为基本从属盘，c为辅助主盘，d为辅助从属盘），“~”代表分区，前四个分区用数字1到4表示，它们是主分区或扩展分区，从5开始就是逻辑分区。例，hda3表示为第一个IDE硬盘上的第三个主分区或扩展分区,hdb2表示为第二个IDE硬盘上的第二个主分区或扩展分区。
- 对于SCSI硬盘则标识为～”，，SCSI硬盘是用“sd”来表示分区所在设备的类型的，其余则和IDE硬盘的表示方法一样

查看所有的设备挂载情况

```shell
lsblk  或者 lsblk -f
```

跳过，以后用到再看

## Linux 网络

**查看系统网络情况**

```shell
netstat [选项]

-an # 按一定顺序排列输出
-p  # 显示哪个进程在调用
```

![image-20240418091243525](https://raw.githubusercontent.com/balance-hy/typora/master/thinkbook/image-20240418091243525.png)



### 网络环境配置

第一种方法(自动获取)：

* 说明：登陆后，通过界面的来设置自动获取ip

* 特点：linux启动后会自动获取IP

* 缺点：每次自动获取的ip地址可能不一样。

第二种方法(固定ip)：

- 直接修改配置文件来制定IP，并可以连接到外网
- 编辑 `vim /etc/sysconfig/network-scripts/ifcfg-ens33`

```shell
TYPE="Ethernet" # 网络类型
PROXY_METHOD="none"
BROWSER_ONLY="no"
BOOTPROTO="none" # 设置为none或static就会用我们指定的ip
DEFROUTE="yes"
IPV4_FAILURE_FATAL="no"
IPV6INIT="yes"
IPV6_AUTOCONF="yes"
IPV6_DEFROUTE="yes"
IPV6_FAILURE_FATAL="no"
IPV6_ADDR_GEN_MODE="stable-privacy"
NAME="ens33"
UUID="a11ce686-fde0-49d8-87f5-ff5b4e5f0794"

DEVICE="ens33" # 设备名
ONBOOT="yes" # 系统启动时网络接口是否有效
IPADDR="192.168.40.129" # 指定ip
PREFIX="24"
GATEWAY="192.168.40.2" # 指定网关
DNS1="192.168.40.2" # 指定 dns 解析器
IPV6_PRIVACY="no"
```

修改完成后重新配置网络，或者重启

```shell
systemctl restart network
reboot
```

### 主机名和hosts映射

为了方便记忆，可以给linux系统设置主机名，也可以根据需要修改主机名

- 指令hostname：查看主机名
- 修改文件在 `/etc/hostname`
-  指定修改后，重启生效

思考：如何通过主机名能够找到(比如ping)某个linux系统?

 windows在 `C:\Windows\System32\drivers\etc\hosts` 文件指定即可

```
案例: 192.168.200.130 hspedu100
```

linux在` /etc/hosts` 文件 指定

```
案例: 192.168.200.1 ThinkPad-PC
```

**什么是DNS？**

DNS，就是 Domain Name System 的缩写，翻译过来就是域名系统是互联网上作为域名和IP地址相互映射的一个分布式数据库。

用户在浏览器窗口输入www.baidu.com

* 浏览器先检查浏览器缓存中有没有该域名解析IP地址，有就先调用这个IP完成解析；如果没有检查操作系统DNS解析器缓存，如果有直接返回IP完成解析。这两个缓存，可以理解为本地解析器缓存
* 一般来说，当电脑第一次成功访问某一网站后，在一定时间内，浏览器或操作系统会缓存他的IP地址（DNS解析记录）
  * 可在cmd窗口中输入 `ipconfig/displaydns` DNS的域名解析缓存
  * `ipconfig /flushdns` 手动清理dns缓存
* 如果本地解析器缓存没有找到对应映射，检查系统中hosts文件中有没有配置对应的域名IP映射，如果有，则完成解析并返回。
* 如果本地DNS解析器缓存和hosts文件中均没有找到对应的IP则到域名服务DNS进行解析域

## Linux 进程

在LINUX中，每个执行的程序都称为一个进程。每一个进程都分配一个ID号（pid/进程号)。

- 每个进程都可能以两种方式存在的。**前台与后台，所谓前台进程就是用户目前的屏幕上可以进行操作的。**
- 后台进程则是实际在操作，但由于屏幕上无法看到的进程，通常使用后台方式执行。
- 一般系统的服务都是以后台进程的方式存在，而且都会常驻在系统中。直到关机才才结束。

**如何终止进程？**

```shell
kill [选项] 进程号
killall 进程名称 # 注意killall还会杀子进程

kill -9 xxxx # -9 强制终止
```

```
pstree [选项]，可以更加直观的来看进程信息常用选项

-p：显示进程的PID

-u：显示进程的所属用户
```

**如何动态监控进程？**

top与ps命令很相似。它们都用来显示正在执行的进程。Top与ps最大的不同之处，在于top在执行一段时间可以更新正在运行的的进程。

```shell
top [选项]

-d  # 代表秒数，默认是3秒
-i  # 不显示闲置或者僵死进程
-p  # 通过指定进程id来监控特定进程

输入后，会在前台开启服务
键入 P ，按cpu使用情况排序
键入 M ，按内存使用情况排序
键入 N ，按 PID 即进程号排序
键入 q ，退出
键入 u ，交互式选择用户占用的进程
键入 k ，键入id，结束指定进程
```

### 服务管理

服务（service）本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其它程序的请求，比如（mysqld，sshd防火墙等），因此我们又称为守护进程，是Linux中非常重要的知识点。

```shell
systemctl [start stop restart reload status] 名称

systemctl enable/disable 名称 # 设置服务开机启动/关闭服务开机自启
systemctl is-enabled 名称 # 查询服务是否为自启动
```

kill进程会容易中断服务本身的临时内存清理，关闭文件等操作 不推荐关闭用kill

打开或关闭防火墙指定窗口

```shell
firewall-cmd --permanent --add-port=端口号/协议 # 打开端口

firewall-cmd --permanent --remove-port=端口号/协议 # 关闭端口

firewall-cmd --reload # 重新载入，才能生效

firewall-cmd --query-port=端口/协议 # 查询端口是否开放
```

## Linux 包管理

Linux的包管理是一种机制，用于在Linux操作系统中管理软件包的安装、更新、卸载和配置。它使用户能够方便地获取、安装和维护软件包，而无需手动下载、编译和处理依赖关系。

### rpm

rpm用于互联网下载包的打包及安装工具，它包含在某些Linux分发版中。它生成具有.RPM扩展名的文件。

RPM是RedHatPackageManager（RedHat软件包管理工具）的缩写，类似windows的setup.exe，这一文件格式名称虽然打上了RedHat的标志，但理念是通用的。

```shell
rpm -qa | grep xxx # 查询已安装的rpm包列表，可以用grep来过滤查找指定包

# 一个rpm包名的基本格式
一个rpm包名：firefox-60.2.2-1.el7.centos.x86_64
名称：firefox
版本号：60.2.2-1
适用操作系统：el7.centos.x86_64表示centos7.x的64位系统
如果是i686、i386表示32位系统，noarch表示通用。

rpm -q 软件包名 # 查询软件包是否安装
rpm -qi 软件包名 # 查询软件包信息
rpm -ql 软件包名 # 查询软件包中的文件
rpm -qf 文件全路径名 # 查询文件所属的软件包

rpm -e 软件包名 # erase 卸载软件包 可以加上参数 --nodeps 强制删除

rpm -ivh rpm包全路径名称 # install verbose提示 hash进度条 安装rpm包
```

### yum

Yum是一个Shell前端软件包管理器。基于RPM包管理，**能够从指定的服务器自动下载RPM包并且安装可以自动处理依赖性关系**，并且一次安装所有依赖的软件包。

```shell
yum install xxx # 下载安装软件
```

## shell编程



## 运行级别

![img](https://raw.githubusercontent.com/balance-hy/typora/master/2023img/202401051958729.png)

在现代 Linux 发行版中，特别是使用 systemd 作为初始化系统的情况下，直接使用 init 命令是不被支持的。系统管理员通常使用其他工具或命令来管理运行级别或切换系统状态。

如果你想要切换到文本控制台或某个运行级别，可以尝试使用以下命令：

```bash
sudo systemctl isolate multi-user.target
```

这将切换到文本控制台，类似于传统的运行级别 3。

## root密码重置

略，详见

> https://blog.csdn.net/m0_63077733/article/details/132579223
