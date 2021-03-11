    仅仅个人配置遇到的坑，所以难免可能有错误。系统是 opensuse Leap 15.2. 因为系统用的人网上没找到太多，所以配置起来很
麻烦，但是对于很多软件包一般 opensuse 都有自己对应的软件包，所以其实用起来还行。不过强烈建议系统等跟着大众走，就像java
永不倒一样。配置环境这种事情很少有技术含量却很花费时间，能避免尽量避免。配置环境之前对所需求环境必须强烈明确细节，否则就会走很多
不必要的路。时间 > everything。

# 如何在 linux 环境中使用 mips 环境

## 如何在 linux 环境中编译 mips 汇编语言
环境要求：java运行环境，能执行jar，最好1.8及以上

操作步骤：在文件列表中下载 mars.jar 文件，在终端运行
~~~bash
java -jar mars.jar
~~~
等待一段时间就可以看见界面的调试器。文件链接：http://courses.missouristate.edu/kenvollmar/mars/。
这里仅利用java运行不依赖平台的特点，这个jar也能在windows和macos（未测试）上使用。

## 如何在 linux 环境中编译 mips C/C++程序
环境要求：暂且无

操作步骤：
首先尽量在自己系统的软件源内搜索mips或者其他相关关键字，对得到的软件搜索或者安装，各个发行版一般都有较好较完整的工具，直接安装
就行。一般有两种方法：
qemu系统模式实现整个mips系统的虚拟，安装编译器然后用qemu用户模式去执行得到的编译文件产生结果。

### qemu系统模式实现整个mips系统虚拟
此步骤需要下载qemu，一般名为
~~~bash 
qemu-system-...
~~~
之类，之后需要下载几个文件，可以从ustc镜像源下载（http://mirrors.ustc.edu.cn/debian/dists/stable/main/installer-mips/current/images/malta/netboot/）
这两个必须文件，然后运行以下命令（https://blahcat.github.io/2017/07/14/building-a-debian-stretch-qemu-image-for-mipsel/）：
~~~bash
qemu-img create -f qcow2 disk.qcow2 20G # 20G比较大，自己设的5G
qemu-system-mips -M malta -m 1G -hda ./disk.qcow2 -initrd ./initrd.gz 
  / -kernel ./vmlinux-4.19.0-14-4kc-malta -append "nokaslr" \
  -nographic
~~~
之后就到安装环节，域名和配置一般用默认，自己的是设空。最后要划磁盘空间怕不知道磁盘里面有什么就停止了下去。

### qemu用户模式加交叉编译环境
mips-suse-linux-gcc是自己的mips gcc编译器。编译过程十分崩溃，完整命令如下，最后还是有错误：
~~~bash
mips-suse-linux-gcc --sysroot=/usr -I/usr/include test.c -o test -L/usr/lib -lc -static
error: crt1.o wrong file format
~~~
qemu用户模式一般是qemu-mips,qemu-riscv这类qemu-虚拟环境名形式, 如果编译成功，运行
~~~bash
qemu-mips test
~~~
即可出现结果，但是自己又出现一个错误
~~~bash
test: cant't allocate memory
~~~
所以就此作罢。
