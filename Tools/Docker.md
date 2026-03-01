

## Docker设计理念:


$\text{Docker}$ $\text{Layer}$（层）是理解 $\text{Docker}$ 效率和设计哲学的核心。它涉及到 $\text{Docker}$ 的**设计理念**、**抽象地位**以及**底层文件系统技术**。

### 1. 设计理念 (Philosophy)

$\text{Docker}$ $\text{Layer}$ 的设计理念是围绕 **效率**、**可复用性**和**最小化传输**。

- **不变性 ($\text{Immutability}$):** 除了最顶层的容器可写层，所有 $\text{Image}$ 层都是只读的，一旦创建就不会改变。这使得 $\text{Docker}$ 镜像的校验和 ($\text{checksum}$) 是可靠的，保证了环境的一致性。
    
- **可复用性 ($\text{Reusability}$):** 鼓励用户将不常改变的操作放在 $\text{Dockerfile}$ 的前面，以便这些底层的层可以被多个不同的镜像复用。
    
- **缓存机制 ($\text{Caching}$):** $\text{Docker}$ 在构建时会缓存每一层。如果 $\text{Dockerfile}$ 中的某一行指令及其依赖文件没有变化，$\text{Docker}$ 会直接使用缓存的层，极大地加速了镜像的构建过程。
    
- **最小化传输 ($\text{Minimal}$ $\text{Transfer}$):** 当您拉取或推送 $\text{Image}$ 时，如果目标机器已经拥有其中的某些层，$\text{Docker}$ 只会传输缺失的层，而不是整个镜像，从而节省了带宽。
    

### 2. 作用地位 (Abstraction)

在 $\text{Docker}$ 的抽象世界中，$\text{Layer}$ 是构成 $\text{Image}$ 的基本单位。

- **镜像 ($\text{Image}$) 的构成：** 一个 $\text{Image}$ 就是一系列**只读层**堆叠在一起的集合。最底层是操作系统基础（如 $\text{Ubuntu}$ 或 $\text{Alpine}$），上层是安装的依赖、应用程序代码等。
    
- **容器 ($\text{Container}$) 的构成：** 一个 $\text{Container}$ 是一个 $\text{Image}$ 加上一个**可读写层**。这个可写层用于存储容器运行时产生的数据和更改。
    
- **$\text{Dockerfile}$ 指令的映射：** 几乎 $\text{Dockerfile}$ 中的每一条指令（`RUN`、`COPY`、`ADD` 等）都会创建一个新的 $\text{Layer}$。
    

$$\mathbf{Image} = \text{Layer}_0 + \text{Layer}_1 + \text{Layer}_2 + \dots$$

$$\mathbf{Container} = \text{Image} + \mathbf{Writable \ Layer}$$

### 3. 具体实现的技术 (Implementation / Reality)

$\text{Docker}$ $\text{Layer}$ 在底层是使用 **联合文件系统 ($\text{Union}$ $\text{File}$ $\text{System}$)** 技术实现的。这种技术允许将多个文件系统或目录**挂载**到同一个挂载点，形成一个统一的视图。

- **核心技术：写时复制 ($\text{Copy}$-$\text{on}$-$\text{Write}$, $\text{CoW}$)**
    
    - **$\text{CoW}$ 机制：** 当容器中的进程试图修改一个文件时，由于下层是只读的，$\text{Docker}$ 不会直接修改只读层。相反，它会将文件从只读层**复制**到最上层的可写层，然后在可写层进行修改。
        
    - **效率：** 只有在第一次修改时才会发生复制，读操作则直接从只读层进行，这大大提高了性能和存储效率。
        
- 实际驱动 ($\text{Storage}$ $\text{Drivers}$):
    
    $\text{Docker}$ 并不自己实现联合文件系统，而是依赖 $\text{Linux}$ 内核提供的各种存储驱动来实现 $\text{CoW}$ 机制。常用的驱动包括：
    
    - **$\text{OverlayFS}$ (最主流):** 这是目前 $\text{Linux}$ 上最推荐和最常用的 $\text{Docker}$ 存储驱动，因为它效率高、速度快。
        
    - **$\text{AUFS}$ (较早使用):** 一种较老的联合文件系统实现，在过去很常用，但现在 $\text{OverlayFS}$ 更多被推荐。
        
    - **$\text{Btrfs}$ / $\text{ZFS}$ (文件系统级别):** 基于特定的文件系统特性实现分层。




### 1.为何使用Docker？

	想要运行某个software服务前，往往需要部署配置大量的环境依赖，这个过程极为繁琐，尤其涉及到在服务器上部署新开发的软件服务时更是如此，而Docker的img可以急速简化这个过程；

	由于Docker使用了Linux的kernel,使用namespace技术，实际上software运行在container上的效率和运行在裸机上差距并不大，Docker可以成为服务器运行各种需要的服务的最佳手段；


### 2.使用方式些命令运行正常， 那么您的学习和工作完全不受 GUI 故障的影响。请继续使用命令行操作。

1.Docker的最佳实践是：一个服务(software)一个container

配置文件：/etc/docker/daemon.json
(可配置远程镜像源等信息)
### 3. Command

1.docker pull img-name
从远程库拉取img文件到本地

2.docker run img-name
利用本地img文件创建containerregistry
\#该方法会让container阻塞当前shell并且不停刷log infor出来；

参数： -d -p -v (按照顺序)

docker run -d img-name   (d是detach,分离模式)能够避免shell阻塞让container后台运行

\#docker创建的container网络与宿主机也是隔离的，无法通过宿主机port端口直接通过浏览器访问container服务；

docker run -p host : container img
该命令的 -p参数制定映射端口信息，将宿主机的port映射到host port,可以通过host port访问container 服务，container host是外界访问container服务的端口号(比如Nginx port为80)

docker run -v hostdir : container_dir
将容器与宿主机的文件目录绑定（也称挂载），由于是虚拟环境，container内部数据修改不会保存到宿主机，最后删除容器后不会有任何数据保存；这样可以保存container内数据到本机；
注：绑定的时候默认宿主机目录覆盖对应容器目录



3.docker ps
显示当前运行的container列表信息，其中ID是系统唯一分配的，name是run时制定(未指定自动分配)
docker images
查询本地docker下载的img文件

4.docker rm/rmi
删除指定的container/img, 后面的参数是通过docker ps/images 得到的 ID信息；
删除正在运行的container需要强制删除：-f    docker -rm -f CONTAINER_IDX
和应用程序日志数据是完全不同类型的。如果它们放在一个卷上，管理起来会很混乱，并且容易因为一个服务的数据被清除而意外影响到另一个服务。
5.docker volume <host_path> <container_path>
创建命名卷，持久化绑定与container的共享数据

4.docker stop

5.docker start




