1. 什么是Docker
   Docker是一个容器化平台，Docker容器包括应用程序及其所有依赖项，作为==操作系统的独立进程==运行。
2. Docker 架构

* 实际上docker使用了常见的CS架构，也就是client-server模式，docker client负责处理用户输入的各种命令，比如docker build、docker run，真正工作的其实是server，也就是docker demon，值得注意的是，docker client和docker demon可以运行在同一台机器上。

3. dockers与虚拟机

* Docker不是虚拟化方法。操作系统级虚拟化的其他工具，虚拟机是一种硬件级别的虚拟化

* Docker最初使用LXC驱动程序，==LXC里面采用的是cgroups来管理资源，并且隔离的手段是采用namespace命名空间==，与虚拟机相比，这种通过划分组来平衡资源冲突的方式代价更小，它不需要指令级别模拟也不需要即使编译。容器直接在本地CPU上运行。

* 命名空间： 每个容器都在自己的命名空间中运行，但使用与所有其他容器完全相同的内核。发生隔离是因为==内核知道分配给进程的命名空间==，并且在API调用期间确保==进程只能访问其自己的命名空间中的资源==

   4.docker镜像

* Docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统就是UnionFS

* 联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录。



5.docker命令

* docker  run
* docker image
* docker build
* docker ps
* docker exec



6.docker 镜像与容器

* 容器是由镜像创建，每一个镜像有多个容器
* 本质在于，运行时会加一个container层，是可读可写的。、



7.Docker提供docker stats和docker事件等工具来监控生产中的Docker