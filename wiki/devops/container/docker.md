# Docker

1. **Docker Volume**

   ![text](img/docker-volume.png)

   > 想要了解Docker Volume，首先我们需要知道Docker的文件系统是如何工作的。Docker镜像是由多个文件系统（只读层）叠加而成。当我们启动一个容器的时候，Docker会加载只读镜像层并在其上（译者注：镜像栈顶部）添加一个读写层。如果运行中的容器修改了现有的一个已经存在的文件，那该文件将会从读写层下面的只读层复制到读写层，该文件的只读版本仍然存在，只是已经被读写层中该文件的副本所隐藏。当删除Docker容器，并通过该镜像重新启动时，之前的更改将会丢失。在Docker中，只读层及在顶部的读写层的组合被称为Union File System（联合文件系统）。
   
   > 为了能够保存（持久化）数据以及共享容器间的数据，Docker提出了Volume的概念。简单来说，Volume就是目录或者文件，它可以绕过默认的联合文件系统，而以正常的文件或者目录的形式存在于宿主机上。

   > volume保存在hosts的/var/lib/docker/volumes/, 这个文件系统不允许被非docker的程序改变

   > 除了volume, docker还有其他持久化方案, 比如Bind mounts直接使用host的文件系统(不建议使用), tmpfs mounts持久化敏感数据到内存

   ```bash
   使用新建volume持久化镜像的/data目录
   docker run -it --name vol-test -h CONTAINER -v /data debian /bin/bash
   ```

   ```bash
   使用已存在volume持久化镜像的/data目录
   docker run -d -v my-vol:/data debian
   ```

   ```bash
   使用主机目录持久化镜像的/data目录
   docker run -v /home/adrian/data:/data debian ls /data
   ```

   ```bash
   共享数据
   docker run -it -h NEWCONTAINER --volumes-from vol-test debian /bin/bash
   ```

   ```bash
   自动删除卷
   docker rm -v xxx
   或
   docker run -rm xxx 

   删除未使用的卷
   docker volume rm $(docker volume ls -q)
   ```

2. **OverlayFS**
   1. 说明
      > 是一种堆叠文件系统, 于2014年正式合并到Linux-3.18主线内核, 它依赖并建立在其它的文件系统之上(例如ext4fs和xfs等等), 并不直接参与磁盘空间结构的划分, 仅仅将原来底层文件系统中不同的目录进行"合并", 然后向用户呈现.
   
   2. 配置github token(Minishift会去github上验证，并调用其api，不配置token将没有api权限)
      ```bash
      export MINISHIFT_GITHUB_API_TOKEN=<token_ID>
      ``