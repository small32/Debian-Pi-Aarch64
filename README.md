# Debian Pi Aarch64

树莓派64位系统

**Debian Stretch Aarch64**

2019 新年版 v1.0 全新正式发布！

[OPENFANS开源社区](http://www.openfans.org)  [树莓派爱好者基地](http://rpifans.cn/)  荣誉出品

----

# 系统介绍

这是为树莓派（Raspberry Pi）**3B/3B+/3A** 所全新构建的：

**Debian 9（Stretch）ARM64(Aarch64) 64位系统镜像**

别问为什么树莓派要用64位系统，64位和32位有什么差别 ？因为树莓派CPU支持64位，但是官方没有，其他的请自行补脑...

该镜像默认没有安装 X-WINDOWS 的桌面环境，但是你可以随意自行安装你喜欢的任何桌面环境，此处所发布的版本是已在企业生产环境下使用并验证的正式版本，系统经过严格测试，完全兼容树莓派 **3B/3B+**（ 3A 未测试，因为硬件规格和 3B+ 一致，理论上通用），原生的有线、无线网卡均可正常使用，所有系统软件包数量几乎可以媲美X86的版本，系统基于原生 **Debian 64位** 从头构建（非任何移植版和官改版本），保证原滋原味

由于和原生树莓派没有关系，所以没有树莓派的相关专有命令（如 **raspi-config** ，**rpi-update** 等 ），请自行修改配置文件（ **/boot/config.txt** ）

树莓派天生和Debian的系统兼容性最好（这离不开Raspbian的功劳，因为Raspbian也是基于Debian构建的，所以树莓派在Debian系下的测试是最完善最充分的，再加上本身Debian系统的强大和稳定，大名鼎鼎的Ubuntu就是基于Debian系最出名和成功的一个发行版），加之个人精力有限，之前所发布的Ubuntu、Centos等系统将不再更新，以后只会以本次发布的系统镜像为基线更新，特此说明

本次系统**完全不同**于之前我们所发布的所有系统(同样也包括之前的64位的Debian )，此次我们**全部从头全新构建**在我们的实验室新构建了全新的自动编译和打包、测试系统，同样我们也对系统重新定义了打包流程和调整了所有的相关配置，对内核进行了大量的修改、调整优化和BUG修复，加入了很多新的功能和特性，特别是加入了**KVM虚拟化**的支持以及**重点加强**了对**Docker**的各项特性支持和优化

本次发布的镜像根据不同的文件系统有3个版本，分别为：***Ext4、BtrFS、F2FS***，老铁们可以根据自己的喜好和场景选择不同的版本进行下载和使用

-------

# 关键特征

## 更新

    1. 系统包更新至Debian稳定主线：2018-12-30
    2. 使用官方Dcoker-CE 版本已更新至：18.09
    3. 固件Firmware已更新至：v1.20181112
    4. 无线固件已更新至：2018-08-20官方版
    5. 系统内核同步更新至Linux主线长期支持：4.14.90

## 系统特性

### 文件系统

本次发布的镜像根据不同的文件系统有3个版本，分别为：***Ext4、BtrFS、F2FS*** ，老铁们可以根据自己的喜好和场景选择不同的版本进行下载和使用

**关于 Ext4、BtrFS 和 F2FS**

* **Ext4**

主流Linux文件系统，经过多年的优化和使用是目前使用**最广泛**的Linux文件系统之一，在大量的企业生产环境中选用，非常的成熟和稳定，支持在线热扩容

* **BtrFS**

新兴的写时复制文件系统，支持更好的数据校验和快照功能，能提高文件系统的时间/空间性能，包括延迟分配，小文件的存储优化，目录索引等，支持在线热扩容

* **F2FS**

专门为 Flash Transition Layer 的 NAND 闪存开发的文件系统，在闪存随机读写性能上有较大提升，对小文件读取也有一定的优化，不支持在线扩容

**考虑到系统的健壮性和稳定，我们推荐使用 Ext4 ！**

### Docker

大名鼎鼎的Docker这里就不做过多介绍了，本次我们重新编译内核，加入了对Dcoker全特性的各项支持，这可能是目前在 **树莓派Aarch64位系统架构下对 Docker 特性支持最完善的版本** ，其他的绝大多数版本都还是32位的，原生的64位系统内核缺少了很多对Docker特性的内核支持

就连大名鼎鼎的**32位**树莓派Docker系统 **hypriot** 最新版也不能完整支持 Dcoker Swarm

由于 **Hypriot** 不支持“ **bridge vlan/vxlan netfiler** ”, 你将得到以下错误信息:

```shell
reexec to set bridge default vlan fialed exit status 1
```

这将导致不能完全正常使用**Dcoker Swarm**集群，网络缺少相关特性支持，会存在一些功能缺失的问题

你可以通过 **systemctl status containerd** 和 **systemctl status docker** 以及查看日志的方式在原生的64位ARM系统上发现不少错误和警告信息，拒不完全统计，原生的系统内核至少包括以下特性的缺失：

     1. cgroup memory limit
     2. cgroup swap limit
     3. cgroup rt period
     4. cgroup rt runtime
     5. cgroup pid support
     6. cgroup memory sysfs
     7. cgroup rdma
     8. cgroup perf
     9. cgroup bridge vlan/vxlan
    10. cgroup netfiler
    11. cgroup net prio
    12. cgroup cfs bandwidth limit
    13. bridge vlan ilter
    14. ipvs
    15. ipvs ipv6
    16. memory sysfs
    ...

考虑到通用性，我们的系统默认没有启用Docker服务，如需要启动Docker服务，请执行以下命令：

```shell
systemctl enable containerd
systemctl enable docker
systemctl start containerd
systemctl start docker
```

执行以下命令查看服务状态：

```shell
systemctl status containerd
systemctl status docker
```

 - Docker Compose

Docker官方没有提供基于arm的**docker-compose**可运行二进制程序，因此我们重新编译并提供了可运行的aarch64位二进制程序

默认没有加入到系统镜像中，你可以按照以下步骤下载并安装：

      1. 前往下载地址下载最新版的 docker-compose aarch64 二进制文件
      2. 解压下载得到的文件
      3. 将下载的文件重命名为 docker-compose
      4. 然后将 docker-compose 文件复制到系统的 /usr/bin/ 目录下
      5. 最后给予文件执行权限即可，执行命令
      6. chmod +x /usr/bin/docker-compose

**docker-compose aarch64** 二进制文件的下载地址：[**点击前往下载**](https://github.com/openfans-community-offical/Debian-Pi-Aarch64/tree/master/docker-compose-aarch64)

**目前最新版本：** v1.23.2

- Docker Machine

Docker官方没有提供基于arm64的**docker-machine**可运行二进制程序，因此我们重新编译并提供了可运行的aarch64位二进制程序

默认没有加入到系统镜像中，你可以按照以下步骤下载并安装：

      1. 前往下载地址下载最新版的 docker-machine aarch64 二进制文件
      2. 解压下载得到的文件
      3. 将下载的文件重命名为 docker-machine
      4. 然后将 docker-machine 文件复制到系统的 /usr/bin/ 目录下
      5. 最后给予文件执行权限即可，执行命令
      6. chmod +x /usr/bin/docker-machine

**docker-machine aarch64** 二进制文件的下载地址：[**点击前往下载**](https://github.com/openfans-community-offical/Debian-Pi-Aarch64/tree/master/docker-machine-aarch64)

**目前最新版本：** v0.16.0

### AUFS

* 什么是AUFS文件系统 ？

AUFS的全称是 advanced multi-layered unification filesystem ，主要功能是把多个文件夹的内容合并到一起，提供一个统一的视图，主要用于各个Linux发行版的livecd中，以及docker里面用来组织镜像

AUFS曾是Docker默认的首选存储驱动，它非常的稳定、有很多真实场景的部署、很强的社区支持，它有以下几个主要优点：
 
1. 极短的容器启动时间

2. 有效的存储利用率

3. 有效的内存利用率

4. 但由于它没有包含在Linux内核主线中，所有很多Linux发行版并不支持AUFS

* 最新的Docker默认使用的是更快最新的OverlayFS文件系统 ，**本系统保持此官方设置的默认值**

* 我是否需要使用AUFS ？

由于Docker的镜像文件使用的文件系统是分成多层存储的，OverlayFS只支持2层，使用同样的镜像层时会复制多个副本，而且占用更多的空间和I/O，而对于AUFS则不一样，它同样提供使用多层存储的功能并共享数据，Docker使用AUFS的 CoW（Copy-on-Write）写时复制技术来实现镜像共享和最小化磁盘空间的使用

对于树莓派这样存储空间较小的设备而言，使用AUFS文件系统能更好的利用我们有限的存储空间资源并降低I/O消耗

**为了用户能够根据自己的情况自由的选择和使用OverlayFS还是AUFS，我们在内核中加入了对AUFS模块的支持**

由于AUFS并不在原生的Linux内核支持中，所以我们通过AUFS的PATH，为主线4.14的内核加入了AUFS的内核模块代码并重新编译，现在系统启动会自动加载AUFS模块，无需人工干预，即可直接使用

### Swap

本次新版系统默认增加了对Swap交换缓存的支持，以缓解系统内存不足所造成的死机问题，大大提升了可执行大容量内存需求应用程序的支持和效率，**用户无需手动创建系统Swap，我们的系统在安装完成后将自动完成并初始化Swap**，Swap文件或分区的位置可以通过查看系统挂载配置文件 **/etc/fstab** 来调整

* Ext4、F2FS 版本系统使用Swap文件方式，便于用户后期根据实际需求调整大小，默认大小为1G

* BtrFS 版本系统由于BtrFS文件系统不支持使用Swap文件，而使用的是Swap分区方式，默认大小为1G，不支持修改默认Swap分区大小

### zSWAP

本次新版系统内核默认已加入了 **zSWAP** 虚拟内存压缩功能，可为将要交换的页面提供压缩回写缓存，默认占用物理内存上限为系统总内存的 **25%**

* **zSWAP的好处**


1. 当内存页将要交换出去时，zSWAP不将其移动到交换设备，而是对其执行压缩，然后存储到系统RAM内动态分配的内存池中，回写到实际交换设备的动作则会延迟，甚至能完全避免，从而显著减少Linux系统用于交换的I/O。对于树莓派这样依托于TF卡的设备，本来I/O就不是很高，减少用于交换的I/O的操作就意味着提高系统性能，把I/O用在系统应用程序等更需要的其他调度上去

2. 在使用Swap文件/分区的场景下，由于减少了对Swap文件/分区的I/O操作，可以提升TF的使用寿命

3. zSWAP并不虚拟一个块设备，而是hook到普通的Swap代码里，在实际发生写入到磁盘/从磁盘读取的操作前，先利用自己管理的内存进行数据的换出/换入，内存不够用以后再使用传统的Swap文件/分区。所以zSWAP适用于本身已经有交换分区的系统，以及树莓派这样本身内存不大的硬件设备

* **为什么不使用zRAM**

zSWAP同时使用内存和交换分区，并根据实际情况自动调整所占内存的大小，所缓存的数据在系统内存不够用时会自动存入到Swap文件/分区中，大大提高了系统swap交换的效率，更充分的利用了内存空间资源又避免了系统出现内存不够的情况，而zRam完全使用内存来进行操作和存储数据，完全占用内存，一般用在大内存的场景，这对于内存很小的树莓派来说就不合适了

* **zSWAP在本系统中使用的内存分配器和数据压缩算法**

**内存分配器zpool框架： smalloc、zbud、z3fold**
      
1. 现实表明，zsmalloc虽然压缩率高，但是算法复杂，还需要额外的线程负责内存整理，耗费更多的cpu时间

2. 而zbud实现简单，以内存page为单位的存储又不会产生内存碎片，但是压缩率低，最高只有2倍，不满小内存系统的实际需求

3. 因此，索尼开发了z3fold内存分配器，和zbud的实现完全一致，只是每个page可以存储3个compressed page，**最高压缩率有 *3* 倍**，逼近zsmalloc的4倍，该技术在xperia xzp的中使用，使4GB的手机获得超过4GB内存的体验
      
我们在系统中选用了z3fold算法，在系统 /boot/cmdline.txt 中可以看到配置：

```shell
zswap.enabled=1 zswap.zpool=z3fold zswap.compressor=lz4 zswap.max_pool_percent=25
```

**压缩算法**

Linux有很多压缩算法：lz,lzo,xz,gzip,lzma... 

我们这里采用了拥有极高压缩速度和较高压缩率的lz4 ，lz4相对于其他压缩算法而言，压缩率不是最高的，但速度确是最快的，在已采用 z3fold 算法的情况下，结合树莓派本身的性能特点和实际的测试，在拥有不错的压缩率的情况下，速度对于树莓派来说才是最重要的（考虑到本身CPU性能开销和内存大小的情况）


### KVM 虚拟化支持

我们在编译内核时加入了对KVM虚拟机的支持，直接支持全虚拟化的方式运行，添加了支持原生的KVM虚拟硬件内核模块的支持，相关的主要内核模块如下：

    1.  virtio //标准虚拟磁盘
    2.  virtio-scsi //虚拟scsi磁盘
    3.  virtio-blk //磁盘直通
    4.  virtio-blk-scsi //scsi模式磁盘直通
    5.  virtio-net //虚拟网卡
    6.  virtio-balloon //内存气泡
    7.  virtio-hw-random //虚拟随机数硬件
    8.  virtio-console //虚拟终端
    9.  virtio-input //虚拟输入设备
    10. virtio-crypto-device //虚拟加密设备
    12. virtio-drm-gpu //虚拟显卡
    13. virtio-9p //目录共享
    14. vfio //设备直通
    15. vhost //主机数据交互
    16. vhost-net  //主机网络数据交互

考虑到系统的通用性，以上模块没有自动加载，需要老铁们自行手动配置加载，具体使用配置请查看KVM相关手册

### UEFI启动

新系统抛弃了传统树莓派的启动方式，使用当下PC流行的EFI方式启动系统，其特性如下：

    1. 纯64位EFI方式引导
    2. 可配置的UEFI BIOS
    3. 虚拟SMBIOS硬件支持（传统树莓派的系统模式下根本就没这玩意儿）
    4. 启动时可进入BIOS进行设置
    5. 支持EFI专有的EL2启动模式以启用KVM硬件虚拟化的支持，大幅度提升KVM性能（需要开机在BIOS里设置，默认使用EL1标准模式）
    6. 支持自定义启动选项：从其他设备、分区、文件等

**提示：如何设置系统启动方式**

    1. 开机按住"ESC"进入"BIOS设置"
    2. 选择"Raspberry Pi Configuration"
    3. 进入"HypDxe Configuration"
    4. 进入"System Boot Mode" 进行选择

该BIOS功能异常强大，还有很多可配置选项，各位老铁请自行发掘 ^_^ :P

    * EFI固件引用自 "andreiw/RaspberryPiPkg" 的项目

### TCP加速

内核默认已开启 **TCP BBR** 拥塞控制算法

* 什么是 **TCP BBR** ？

我们还是简答来说 TCP BBR 解决了什么问题吧

    1. 在有一定丢包率的网络链路上充分利用带宽，非常适合高延迟、高带宽的网络链路
    2. 降低网络链路上的 buffer 占用率，从而降低延迟，非常适合慢速接入网络的用户
    
简单来说使用 TCP BBR 拥塞控制算法能够在一定程度上提升TCP传输速度和效率，优化网络传输

* 如何关闭

编辑 “/mnt/etc/sysctl.conf” 文件，执行命令 "sysctl -p" 查看结果，看不到以下注释掉的内容，代表关闭成功    
注释掉以下2行内容： (前面加上#即可)

```shell
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```

### VC4 图形双栈切换

* 什么是VC（VideoCore）

VideoCore 是一个由Alphamosaic Ltd开发并且现在被Broadcom拥有的低功耗移动多媒体指令集架构。二维的DSP架构使其在软件中可以灵活且高效地编解码多媒体数据的同时保持低功耗。该IP核目前仅在Broadcom的SoC中被使用

简单来说VC就是树莓派的视频硬件解码加速芯片，VC4 就是指第四代架构

由于树莓派使用的 VC4 没有发布硬解码部分的代码，所以如果要使用硬件解码（HardFP）加速功能只能使用 VC32 硬件加速模式 (**我们的系统支持在64位模式下运行VC32硬件加速模式**)

**注意：如果需要使用VC32 硬件加速模式请参照后面的设置，开启显卡3D硬件加速**

* 使用方法

我们提供了系统VC4图形模式的双栈切换功能，非常方便

支持 VC32 硬件加速模式（HardFP）和 vc64 原生64位的兼容模式切换，**系统默认使用原生vc64兼容模式**

如需切换为vc32硬件加速模式，只需要执行命令：

```shell
enableVChardfp
```

切换回vc64兼容模式，请执行命令：

```shell
enableVC64
```

**切换模式后需要重启系统生效**

### CPU硬标支持

在arm64的Linux主线内核上，已经不再支持在 " **/proc/cpuinfo** " 中显示硬件型号名称，而是使用DMI接口来获取相关信息，这就造成了树莓派的很多程序如 **GPIO Wiringpi** 在运行时检查不到设备的硬件型号，提示不支持的硬件，无法运行，我们修改了Arm64内核的相关文件，提供了CPU硬标功能的支持，现在可以同样在64位系统下显示硬件的型号了

截图！！

----

# 配置及说明

系统的其他配置如下：

### 默认账户

    用户名：pi ，密码为: raspberry 

### 安全增强

  1. 不再设置默认的 **root** 用户密码，需要用户使用默认 **pi** 账户登录后通过 " **sudo passwd root** " 命令自行设置 **root** 用户密码
    
  2. **默认没有开启 root 用户的ssh登录权限（默认仅支持 pi 用户远程ssh登录）**

  3. 不再允许初始保留使用默认密码，首次使用 **pi** 账户登录后需要重新设置默认账户pi的密码：

    首次登陆后首先再次输入pi账户的默认密码 raspberry

    然后按照系统提示重新设置pi账户密码并再次输入确认

### 默认仓库源为清华源
     
    用户可自行配置为其他源

### 32位软件支持

    系统默认已开启对32位软件包的支持，无需手动添加，安装32位软件请在包的名称后加入后缀 ":armhf" 
    
    例如: apt install ibc6:armhf  // 安装32位的ibc6

    注意：这里的冒号 ":" 是需要的哦

### 启动模式

* 三种文件系统格式的镜像都支持 **开机自动扩展根分区**

* 第一次开机启动会自动重启并扩展 **root** 根分区（第一次安装系统会重启**2**次，第一次检查文件系统，第二次扩展root根分区，所以需要等待一些时间）

* 两次重启后，正常引导系统，在这一次启动过程中会自动配置和生成Swap文件并完成最后的**系统初始化**，这将消耗一段时间（仅一次），待完成Swap交换文件的设置后，即可进入到登录界面

* 如果使用的是BtrFS文件系统格式的镜像在最后完成系统初始化时不会创建Swap交换文件，所以无需等待

* 因为正如前面文中提到的BtrFS文件系统不支持使用Swap交换文件的模式而只能使用交换分区的方式，考虑到这一点，我们在制作镜像的时候就已经默认创建并配置好了Swap交换分区，所以当然不需要了，这就是为什么BtrFS文件系统镜像会比其他两个镜像都大一些并且有3个分区（其中一个1G大小的是默认的交换分区）的原因 :P

      * 自动扩容脚本部分参考了树莓派官方和 UMRnInside/RPi-arm64 项目的部分内容 

### 预配置项

系统提供网络和自动开机任务的预配置，相关配置文件的路径和对应关系如下：

| 预配置项 | 预配置文件路径 | 对应链接到的系统文件路径 |
| --- | --- | --- |
| 无线网络 | /boot/wlan0 | /etc/network/interfaces.d/wlan0 |
| 有限网络 | /boot/interfaces | /etc/network/interfaces |
| DHCP客户端 | /boot/dhclient.conf | /etc/dhcp/dhclient.conf |
| 自定义启动脚本 | /boot/rc-local | /etc/rc.local |

#### 网络预配置

镜像支持在启动前提前配置好网络（无线、有线网络均可配置）

* 无线网络配置 /boot/wlan0

```shell
# Used this conf, unconfig # every line bellow. (Do not change this line)
#除第一行外（第一行可以删除，去掉一下每行的“#”注释符号，中文内容是注释，删除或不要取消前面的“#”符号）

#auto wlan0
#allow-hotplug wlan0
#iface wlan0 inet dhcp

#wpa-ssid "Your-Wifi-SSID-Name"
#修改此处“Your-Wifi-SSID-Name”为无线的SSID名称

#wpa-psk "Your-Wifi-SSID-Password"
#修改此处“Your-Wifi-SSID-Password”为无线密码

#注意，以上两行的双引号需保留
```
    
* 有线网络配置 /boot/interfaces

```shell
# interfaces(5) file used by ifup(8) and ifdown(8)
# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d
## Used dhcp ip address set eth0 inet to dhcp,
## or used static ip address set eth0 to static
## and change other ip settings.
## If you wanna let settings to take effect,
## uncomment symbol in front.

#auto eth0
#allow-hotplug eth0

#iface eth0 inet dhcp
#iface eth0 inet static
#address 172.16.192.168
#netmask 255.255.255.0
#gateway 172.16.192.1
#dns-nameservers 8.8.8.8
```

#### 自动任务预配置

系统支持自定义任务自启动脚本，可以在系统启动前预先配置

    编辑脚本文件 "/boot/rc-local" ，加入自定义的脚本内容

### 其他杂项配置

#### 开启armel支持

执行以下命令

```shell
dpkg --add-architecture armel  
apt-get update
apt-get install libc6-armel
```

#### 安装桌面

这里以安装mate桌面环境为例：

执行以下命令，然后重启即可

```shell
sudo apt-get install --no-install-recommends xserver-xorg -y
sudo apt-get install mate-desktop-environment-core lightdm -y
```

#### 开启显卡3D硬件加速

编辑 /boot/config.txt 文件，在文件末尾加入：

```shell
dtoverlay=vc4-fkms-v3d
gpu_mem=256  （这个值最小应应保证不低于128）

注意：
vc4-fkms-v3d 是较好的优选兼容模式
也可以设置为 vc4-kms-v3d 全硬件加速，但是在远程等环境下显示会有一些问题
除非你是本地接显示器，一般不建议设置为 vc4-kms-v3d
```

然后重启即可

----

# 系统测试

一个不完全的测试结果，只是理论数据，仅供参考

使用 Ext4 标准文件系统和 树莓派3B+ 的 UnixBench 测试结果（和同类Debian Aarch64系统相比）：

综合整体性能全面超越对比测试系统的2~3倍以上，部分指标甚至达到了10倍以上，1000%的提升

----

# 系统截图

启动界面

UEFI BIOS 界面

----

# 系统由来

首先感谢一位先驱，来自国外 **bamarni** 大神发布了开源的树莓派64位系统 **pi64** ，正是他的无私奉献和开源分享的精神，开创了树莓派64位系统的先河，仅以此系统对bamarni大神表示崇高的敬意和感谢

本来之前就打算说一说这个系统的由来的，忘记了，这次补上，先给大家上张图：

以上截图来自于国内某树莓派64位系统论坛管理员对发布的树莓派64位系统介绍的部分摘要，注意以上红色框注部分

我们来自一个忠实的开源爱好者社区，看到上诉内容的时候感到十分的震惊和愤怒，对这位所发布的64位系统进行了下载和深度测试，结果发现了一些问题…
 
然而这位 “牛B的大神” 居然连无线这种基本问题都没有解决就叫嚣着如何如何牛掰，后续冒似长时间都没有解决这些关键问题，然而出自该“大神”之手的一些列系统后来宣称 “**对其树莓派3B+的无线问题无解**” ，一边打着“优化”的口号，自持着所谓的牛掰能力，做了一些所谓的优化，上来叫嚣着 “**吊打**” 同类系统之类的言语。结果，我们发现不管是我们的测试结果，还是他自己的很多用户都发现了所谓 “**优化**” 后的系统所带来的诸多问题…

正好我们社区内部也有使用树莓派64位系统的需求，加上处于对开源精神的崇高敬意和对这位 “牛掰大神” 的愤怒，我们发布了我们自己的树莓派64位系统，并通过与“树莓派爱好者基地”合作独家发布，也就是之前发布的一系列64位系统

对于这种 **无知的、不知天高地厚、恬不知耻、狂妄自大的 “大神”**，真是嗤之以鼻，对于国外大神他给予的 “**辣鸡**” 评价，叫嚣着 “**吊打**” 同类系统的行为表示愤慨和同情，同情他的**无知、可悲、可怜**


对技术要抱有**敬畏之心**，开源，取之不易，理为传承分享和奉献，我们仅记，小心翼翼，如履薄冰，虚心谨慎，不断进步


* **必要的更新说明**

对于“狂妄之徒”言论的一些说明，仍然是那位，这里就不点名了，玩家自行补脑，真是厚颜无耻，还是先上一个截图：

为了避免不明真相的吃瓜群众被误导，这里讲下这货钻了什么空子，他采用非EFI模式引导，这种模式下树莓派的标准内核是支持显示Hardware内容的，而以EFI模式启动的系统采用的是全新的EFI引导模式，抛弃了传统的方式，包括原生aarch64架构的ARM64系统都是不会到 /proc/cpuinfo 下写入信息的

关于这点，已经在上面 “ **CPU硬标支持** ” 的部分说明过了，这不是什么问题，也不是什么BUG！我们的系统也支持在 /proc/cpuinfo 下的CPU硬标显示，这没有什么好炫耀的，**只是实在看不惯这位“牛人”说话的口气**，使UEFI用的是虚拟的SMBIOS，所以在EFI引导模式下，查看CPU信息的正确做法是：

执行命令

```shell
dmidecode -t 4
```

如上图所示，dmidecode命令是专门读取SMBOIS硬件信息的命令，通过该命令我们能够获取有关CPU更详细的信息情况，而不是 cat /proc/cpuinfo 传统方式这些简单的信息，我们甚至能看到CPU支持的频率，CPU支持的特性，例如上图可以看到SMBIOS开启了CPU虚拟化的支持，这些都是 cat /proc/cpuinfo 传统模式所不具备的

另外必须要说明的是，在传统模式下，非EFI引导的方式是无法使用 **dmidecode** 命令获取信息的，因为它根本就没有BIOS！对于某些别有用心的人误导群众，指鹿为马，这里必须要站出来说明一下，以免大家被误导！

----

# 版权说明

1. 以上系统由 "**OPENFANS开源社区**" 制作，仅由 “**树莓派爱好者基地**” 独家发布并提供技术支持

2. 任何文字、图片转载必须标明系统（软件）来源；您可以对软件或系统进行任何修改，但必须保留出处；严谨用于任何商业用途，如需商业使用，请与 “**OPENFANS开源社区**” 及 “**树莓派爱好者基地**” 联系并获得许可

3. 以上系统以及软件的所有权归属相对应的软件作者和遵守相关软件包的许可授权协议

4. 未遵守上诉规定保留出处，“**OPENFANS开源社区**” 及 “**树莓派爱好者基地**” 有权追究其相应责任并责令停止一切侵权行为

5. “**OPENFANS开源社区**” 及 “**树莓派爱好者基地**” 拥有以上内容的最终解释权
