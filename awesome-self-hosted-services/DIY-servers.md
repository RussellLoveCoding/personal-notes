# 小小 IDC DIY

[toc]

## 引言

玩私服的同好们可能曾经走过 在 PC 上启动服务、改装手机、白嫖、购买 PAAS 的服务器资源 的各种路子。但是无论如何，矛盾定律始终存在。

![img](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\a5a369b5543c7781.png)

生物演化史、软件工程演化史告诉我们，一个物种或者软件的演化如果是朝着大而全的方向，会长出很难看的样子，但是若如同野生动物一样，猎豹，朝着某个方向演化，占据特定生态位，才具有美感。但我们的选择更重要是 平衡。

![什么比猎豹的速度更快？ - 趣发现- 故事365](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\liebao.jpg)

私服根据目前浅薄的经验，可大致分为几个用途：

1. 实验型机器；
2. 稳定型服务器；
3. 桌面级办公 PC；
4. 低功耗网络流量转发。

前三个通过搭建家庭服务器解决，第四个则花钱购买节点 提供流畅的网络体验。

本文主要记录个人在组建 私人服务器过程中的实践及其遇到的问题。

由于海鲜市场里，AMD  epyc 7d12, 7542,  Intel E5 2690 v4 CPU 过于香， 围绕它 DIY 服务器 是很有看头的。

上述三款 CPU 的特点如下：

1. epyc 7d12: 超级便宜 600CNY ；低功耗 85W;  32 核 64 线程。 适合做 NAS 等服务。
2. epyc 7542: 超级便宜 2600CNy；单核基准频率 2.9Ghz (7nm 制程); 32 核 64 线程。适合多开虚拟机 做云电脑服务器。
3. intel E5 2690v4：超级便宜 280CNY；基准频率 2.6 Ghz(E5系列最高频率的CPU了，14nm 制程); 14 核心 28 线程；适合做个小服务器, 远程开发机。

配上 二手 机箱、内存，硬盘，一手电源、主板 等，价格十分平民

此外 GPU 根据最近了解消费级可简单分为几个等级：

1. 亮机卡 需求：100以内
2. 高码高清高分辨率 视频流的渲染 需求：300
3. 游戏/渲染 需求：2k ~, 上不封顶
4. 深度学习训练模型：4k ~, 上不封顶

而专业 Tesla GPU 则可分为

1. **P4**，2500+CUDA 核心，5.5 TFLOPS（单精度浮点运算）。与 **GeForce GTX 1080** 相近但更便宜。300+。功耗与 Gtx 1050 相近，满载 75W.
2. **P40**, 接近但低于 **Rtx 2080 Ti** ，但价格相比 **2080 Ti** 腰斩。
3. 当算力继续上升到 消费级 GPU 30系列的层次，专业GPU 的价格飙升，低水平 专业 GPU 的低价源于服务器硬件淘汰和挖矿潮过后流入二手市场成为垃圾

接着下文从 最便宜的 CPU E5 2690 v4 开始练手实践.

## 搭建远程个人云电脑/Homelab

整个过程涉及硬件平台的搭建、和软件平台的搭建，本文只涉及必要的知识，可能对新手不一定友好，新手需要先了解相关知识

### 硬件搭建

至 2023年，近乎10年前开始发布的系列 CPU 里面，应该选择代数尽量高，单核性能最高的，有两个原因：

1. [**代数越高**，制程越先进，**能耗比越高**](https://www.zhihu.com/question/270433294)；
2. [单核性能越高，越适合于胜任个人 PC 用途](https://www.zhihu.com/question/48275271)，且核多，跑并行任务也可。
3. 核心数 8个起。

根据该原则，很容易定位到想要的 CPU, 例如 e5 2667 v4 3.2Ghz 8 核心, 例如[百度贴吧里某位网友推崇](https://tieba.baidu.com/p/8422610723)的 E5 2690 v4 2.6 Ghz 12核心，再看看 CPU 利用率常年徘徊于 20% 左右，GPU 是 0， 就是好说我的电脑有至少 2/3 的成本在吃灰，因此同系统多账户或者多开虚拟机，远程共享硬件资源是个更低成本的解决方案。

由 该 CPU 的配置可延伸出其使用场景：个人 远程云电脑；远程开发机；家庭私服中心；媒体播放器。

硬件购买主要参考如下原则。

1. CPU: 上文已述
2. GPU: 需求为亮机和播放高码率原盘视频，1050 2G 可以应对；
3. 内存：内存管饱即可，不是打游戏，不需要很高的频率，只需要容量管饱，选择具有容错性的二手服务器 内存
4. 存储：固态+机械硬盘的组合。
5. 电源：根据 CPU 和 GPU 的功耗估算，选额500W 的电源。

关于二手和全新的选择的原则，主要是性能、需求、和成本之间的平衡：

1. CPU, GPU, 内存, 机箱 采用二手服务器成本降成本，通过压力测试来测试其可用性。
2. 存储：固态硬盘采用 512G 全新固态，保证 操作系统和软件二进制文件存储在高速存储介质，同时由于固态是有寿命的，因此选择全新；机械硬盘选择 咸鱼上的 生产日期较新的 翻新黑盘；
3. 电源、主板 出于安全性和可用性的考虑选择全新。

最终根据 [瓜皮 Up 主 的视频](https://www.bilibili.com/video/BV1sv4y1c7VG)以及 淘宝店家客服的 指导列出如下硬件采购清单。

| 组件   | 价格        | 品牌及其型号                                       | 参数                                 |      |
| ------ | ----------- | -------------------------------------------------- | ------------------------------------ | ---- |
| 主板   | 330         | 华南 x99 qd4                                       | 单路；<br />尺寸：245*190MMM-ATX板型 |      |
| CPU    | 275         | Intel E5 2960 v4                                   |                                      |      |
| GPU    | 450         | (nvidia tesla p4+涡轮风扇)+亮机卡                  |                                      |      |
| 内存   | 3*90 = 270  | 三星 DDR4 2133 16G * 3                             |                                      |      |
| 硬盘   | 279+400=679 | 致钛 512G  固态 + 2 * WD 3T 黑盘(生产日期为2020年) | 固态 m.2 机械硬盘 3.5                |      |
| 散热器 | 99          | 利民 AX120 2010 定制版                             | 长125mm * 宽 71mm * 高 158mm         |      |
| 电源   | 249         | 长城 6000DS直出线 500W                             | 尺寸：150mm * 140mm * 86mm           |      |
| 机箱   | 100         | 傻瓜超人 手提式 K99 air                            |                                      |      |
|        |             |                                                    |                                      |      |
| 总价   | 2328        |                                                    |                                      |      |

在所有的配件到齐以后，开始组装电脑，安装时有一定顺序：

1. 首先连接主板、CPU、CPU 散热器、内存条、固态硬盘
2. 在主板安装到机箱前，安装把手、风扇到机箱，这样可以留有空间拧螺丝；
3. 安装主板到机箱, 接着安装 GPU 到主板上；
4. 最后安装电源、机械硬盘。

要注意不同螺丝用于不同地方的连接紧固，可以问商家要教程。

最终效果如图：

<img src="D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\803b9b28b5bbfc64d63611470a23bc8.jpg" alt="803b9b28b5bbfc64d63611470a23bc8" style="zoom: 25%;" />

也有其他的低功耗 NAS 方案，如采用

### 开源虚拟化解决方案

在搭建好硬件以后，受启发于现今的云服务，决定自己搭建一个虚拟化管理平台。实际上对硬件资源的综合利用自计算机发明之初就有，计算机的多账户机制就是让多人使用电脑如同各自拥有一台电脑一样，底层操作系统的进程隔离能很好提供这种服务，如今的 容器 技术从 硬件、网络、文件系统、进程 几个角度做了隔离，使得部署、运维更加方便。而在电脑上，早就可以通过 VirtualBox vmware等二类虚拟机监控器 安装虚拟机，体验虚拟化的便利。

为了榨干硬件资源，提供便利的使用，我尝试了 PVE。

> 最初本来打算使用 vmware sphere, 无奈网卡不支持，后来查询 kvm, openstack, pve 等虚拟化技术，锁定了 pve。 kvm 技术 属于 Linux 内核，PVE 是基于它在小规模场景下的解决方案，openstack 是云厂商的方案，用来卖资源，配置运维非常复杂。个人使用 PVE 难度比在 windows 上使用 virtualbox, hyper-v, vmware 要难点，但是学习曲线更多在初次的配置，后期的创建跟 在 windows 上创建虚拟机无异。

Proxmox Virtual Environment（PVE）是一款开源的虚拟化平台，它基于Debian操作系统，并集成了[KVM虚拟化](https://aws.amazon.com/cn/what-is/kvm/)和LXC容器技术。PVE提供了强大的功能和管理工具，使您可以轻松地构建、管理和监控虚拟机和容器。

它属于[一类虚拟机监控器](https://aws.amazon.com/cn/compare/the-difference-between-type-1-and-type-2-hypervisors/)，由于它的架构设计是使虚拟机尽可能直接访问硬件，而非宿主操作系统协调 子虚拟机与自身对硬件资源的利用，性能损失更小。且属于开源方案，具有社区支持。

以下通过 PVE 来安装虚拟机。首先安装 PVE 系统，其实也较为简单，如同常规的重装系统一样，记得插上网线，因为需要连网安装工具

1. 下载 [系统 iso](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso/proxmox-ve-8-1-iso-installer), 通过 rufus 一键制作 系统 U 盘

2. 开机按 "DEL" 键 进入主机BIOS，设置启动 为 USB 启动

3. 进入安装界面

4. 依照提示配置：

   1. 同意 TOS, 按钮在右下角
   2. 时区：选择中国，亚洲上海
   3. 密码：配置自己的密码
   4. IP: 默认即可
   5. 一直点下一步，即可

5. ssh 登录界面 `ssh root@ip` 密码为刚创建的，通过如下工具优化pve 系统，装些常见的工具

   ```bash
   export LC_ALL=en_US.UTF-8
   apt update && apt -y install git && git clone https://github.com/ivanhao/pvetools.git
   cd pvetools
   ./pvetools.sh
   ```

   这里也给出个人的脚本，用来装些 neovim, zsh, tmux等常见工具，使用起来舒服点

   ```bash
   git clone https://github.com/RussellLoveCoding/modern-linux-env-init.git
   cd modern-linux-env-init/installer
   ./modern_linux_env.sh
   ```

6. 用浏览器打开 PVE 管理界面 `https://192.168.100.2:8006`

###  [进一步对PVE进行简单的配置、调优](https://zhuanlan.zhihu.com/p/614820460)

目标是：提高性能，提高资源利用率，降低功耗

### 资源分配与管理

术语解释(参考帮助页面: https://PVE-IP:8006/pve-docs/chapter-qm.html#qm_cpu)

1. CPU 选项中的插槽，相当于物理机的 单路双路的意思，有多少个CPU安装位；

2. CPU 核心数

3. CPU 限制，vcpu 能用到 物理 CPU 的时间百分比, 假如1核物理CPU 创建了个四核心虚拟机，CPU 限制是100%，那么理论上是 400%。当然你不能有一个 CPU 空跑一直占用着，这都是建立在虚拟化多线程的基础上。

4. NUMA

   > 您还可以选择在虚拟机中模拟 NUMA [7] 架构。 NUMA 架构的基本原理意味着，内存不是为所有核心提供可用的全局内存池，而是分布在靠近每个插槽的本地存储体中。这可以带来速度提升，因为内存总线不再是瓶颈。如果您的系统具有 NUMA 架构 [8]，我们建议激活该选项，因为这将允许在主机系统上正确分配 VM 资源。在虚拟机中热插拔核心或 RAM 也需要此选项。

CPU 与 GPU 虚拟化：

CPU 虚拟化无门槛，但是 GPU 虚拟化到 2023 年仍然有诸多门槛， 如



VM 硬盘空间分配：

```bash
# 查看磁盘路径，根据虚拟机 ID 定位到对应的磁盘
lvdisplay

# 缩减磁盘大小
lvreduce -L -10G /dev/pve/vm-801-disk-0
# 调整之后 PVE 页面显示并没有更新，实测可以在页面操作再增加 1G 的方式触发更新

# 扩展磁盘大小
lvextend -L +100G /dev/pve/vm-801-disk-0
```



### 能耗优化与自动化管理

[实现路由器启动主机](https://www.right.com.cn/forum/thread-8239848-1-1.html)

1. 咨询主板商家看是否支持, 并打开对应选项，有两个选项. 
   - `BIOS -> Advanced -> Network Stack Configuration -> Network Stack [Enabled]`
   - `BIOS -> Advanced -> Network Stack Configuration -> Lan Wake up Control [Enabled]`
2. 安装依赖 `apt-get install ethtool`，检查 BIOS 是否成功配置好了  `ethtool enp6s0`， 找到网卡信息中的supports wake-on和wake-on两个参数，如果supports值为pumbg，表示网卡支持远程唤醒,wake-on的值d表示禁用、g表示开启，默认为d。
3. 系统开启网络启动，需要开启 Wake-on-Lan 选项 `ethtool -s enp6s0 wol g` , 将该命令配置到系统自启服务中，开启后通过 `systemctl status rc-local.service` 查看服务是否正常，同时也可通过 ` ethtool enp6s0` 命令验证.

```bash
# 创建脚本 WOL 使能脚本
cat << EOF | sudo tee /etc/rc.local
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
#用ethtool开启网卡远程唤醒
/sbin/ethtool -s enp6s0 wol g
exit 0
EOF

# 修改脚本为可执行文件
sudo chmod +x /etc/rc.local

# 将其设置为守护进程


cat << EOF | sudo tee /lib/systemd/system/rc-local.service
[Unit]
Description=/etc/rc.local Compatibility
Documentation=man:systemd-rc-local-generator(8)
ConditionFileIsExecutable=/etc/rc.local
After=network.target
[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
StandardOutput=tty
RemainAfterExit=yes
SysVStartPriority=99
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable rc-local.service
sudo systemctl start rc-local.service

```

4. 测试是否能通过网络启动，在 PVE 上执行如下命令你个

```bash
# 记下 mac 地址
ifconfig  enp6s0    | sed -rn 's~\s+ether ([^ ]+) .*~\1~p'
# 0a:e0:af:f2:27:f6 

# 关机测试
poweroff
```

等待 PVE 关机，在另外一台主机上，执行如下命令先安装 wol, 后发送"魔术数据包"（Magic Packet）来唤醒主机。

```bash
sudo apt update
sudo apt install wakeonlan
wakeonlan 0a:e0:af:f2:27:f6
```

通过一个小脚本 `wakeup` 实现交互式唤醒

```bash
#!/bin/bash

# Define the MAC addresses for each host
pve_mac="0a:e0:af:f2:27:f6"
home_server_mac="68:da:73:a6:1e:00"

# Display the menu to the user
echo "Please select a host to wake:"
echo "1) pve"
echo "2) Home Server"
read choice

# Handle the user's choice
case $choice in
    1)
        echo "Waking PVE node..."
        wakeonlan $pve_mac
        ;;
    2)
        echo "Waking Home Server..."
        wakeonlan $home_server_mac
        ;;
    *)
        echo "Invalid choice."
        ;;
esac
```



| 类别                     | 可用 | 说明                                                         |
| ------------------------ | ---- | ------------------------------------------------------------ |
| GRID P40-12Q(nvidia-52)  | 2    | num heads=4, frl config=60framebuffer=12288M.max resolution=7680x4320max instance=2 |
| GRID P40-24Q (nvidia-53) | 1    | num heads=4, frl config=60framebuffer=24576M.max resolution=7680x4320max instance=1 |
| GRID P40-1A(nvidia-54)   | 24   | num heads=1 frl config=60framebuffer=1024M.max resolution=1280x1024.max instance=24 |
| GRID P40-2A(nvidia-55)   | 12   | num heads=1, frl config=60framebuffer=2048M.max resolution=1280x1024 |





### **硬盘的直通和休眠**

通过sata控制器 实现硬盘直通，有其[优势](https://foxi.buduanwang.vip/virtualization/1754.html/)：

1. 可以休眠，检测硬盘健康度等，充分利用nas系统优势
2. 虚拟机关机时，硬盘是彻底断电，并非休眠。省电的同时，节省机械硬盘的寿命

[开启硬件直通](https://foxi.buduanwang.vip/virtualization/pve/561.html/)

1. 检查是否支持Vt-d功能：

   通过 BIOS 查看 或者 执行命令 `dmesg | grep -e DMAR -e IOMMU` 得到结果让，chatgpt 辅助检查，例如：
   > ```bash
   > ❯ dmesg | \grep -e DMAR -e IOMMU
   > [    0.009958] ACPI: DMAR 0x000000007624FBF0 0000DC (v01 ALASKA A M I    00000001 INTL 20091013)
   > [    0.009980] ACPI: Reserving DMAR table memory at [mem 0x7624fbf0-0x7624fccb]
   > [    0.284468] DMAR: Host address width 46
   > [    0.284469] DMAR: DRHD base: 0x000000fbffd000 flags: 0x0
   > [    0.284475] DMAR: dmar0: reg_base_addr fbffd000 ver 1:0 cap 8d2008c10ef0466 ecap f0205b
   > [    0.284477] DMAR: DRHD base: 0x000000fbffc000 flags: 0x1
   > [    0.284481] DMAR: dmar1: reg_base_addr fbffc000 ver 1:0 cap 8d2078c106f0466 ecap f020df
   > [    0.284483] DMAR: RMRR base: 0x000000773ee000 end: 0x000000773fefff
   > [    0.284484] DMAR: ATSR flags: 0x0
   > [    0.284486] DMAR: RHSA base: 0x000000fbffc000 proximity domain: 0x0
   > [    0.284488] DMAR-IR: IOAPIC id 1 under DRHD base  0xfbffc000 IOMMU 1
   > [    0.284490] DMAR-IR: IOAPIC id 2 under DRHD base  0xfbffc000 IOMMU 1
   > [    0.284491] DMAR-IR: HPET id 0 under DRHD base 0xfbffc000
   > [    0.284492] DMAR-IR: x2apic is disabled because BIOS sets x2apic opt out bit.
   > [    0.284493] DMAR-IR: Use 'intremap=no_x2apic_optout' to override the BIOS setting.
   > [    0.285084] DMAR-IR: Enabled IRQ remapping in xapic mode
   > ```
   >
   > 从你提供的输出中，我们可以看到你的系统支持 VT-d（Intel Virtualization Technology for Directed I/O）功能。
   >
   > 以下是一些关键信息：
   >
   > 1. `ACPI: DMAR 0x000000007624FBF0 0000DC (v01 ALASKA A M I    00000001 INTL 20091013)`：这一行表示你的系统支持 DMAR（DMA Remapping），这是 VT-d 的一部分。
   >
   > 2. `DMAR: Host address width 46`：这一行表示你的系统支持 46 位的物理地址空间，这对于大内存系统来说是必要的。
   >
   > 3. `DMAR: DRHD base: 0x000000fbffd000 flags: 0x0` 和 `DMAR: DRHD base: 0x000000fbffc000 flags: 0x1`：这两行表示你的系统有两个 IOMMU（Input/Output Memory Management Unit）组，这是 VT-d 的一部分。
   >
   > 4. `DMAR-IR: Enabled IRQ remapping in xapic mode`：这一行表示你的系统支持中断重映射（IRQ remapping），这是 VT-d 的一部分。
   >
   > 总的来说，这些输出表明你的系统支持并已启用 VT-d 功能。

2. 执行命令, 注意这里不同设备不一样。如果是 amd 处理器需要修改 `intel_iommu` 为 `amd_iommu`

   ```bash
   ❯ sed -rin 's~^(GRUB_CMDLINE_LINUX_DEFAULT.*)~# \1\nGRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt video=vesafb:off video=efifb:off video=simplefb:off"~g;' /etc/default/grub
   ```
   执行命令后 `/etc/default/grub`  中的对应行的内容为
   ```bash
   ❯ cat /etc/default/grub | grep GRUB_CMDLINE_LINUX_DEFAULT
   # GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_pstate=disable"
   GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt video=vesafb:off video=efifb:off video=simplefb:off"
   ```

   执行  `update-grub` 使其生效
   ```bash
   ❯ update-grub
   Generating grub configuration file ...
   Found linux image: /boot/vmlinuz-6.5.11-7-pve
   Found initrd image: /boot/initrd.img-6.5.11-7-pve
   Found linux image: /boot/vmlinuz-6.5.11-4-pve
   Found initrd image: /boot/initrd.img-6.5.11-4-pve
   Found memtest86+x64 image: /boot/memtest86+x64.bin
   done
   ```

3. 加载相应的内核模块

   ```bash
   ❯ echo vfio >> /etc/modules
   ❯ echo vfio_iommu_type1 >> /etc/modules
   ❯ echo vfio_pci >> /etc/modules
   ❯ echo vfio_virqfd >> /etc/modules
   
   ❯ update-initramfs -k all -u
   update-initramfs: Generating /boot/initrd.img-6.5.11-7-pve
   Running hook script 'zz-proxmox-boot'..
   Re-executing '/etc/kernel/postinst.d/zz-proxmox-boot' in new private mount namespace..
   No /etc/kernel/proxmox-boot-uuids found, skipping ESP sync.
   Couldn't find EFI system partition. It is recommended to mount it to /boot or /efi.
   Alternatively, use --esp-path= to specify path to mount point.
   update-initramfs: Generating /boot/initrd.img-6.5.11-4-pve
   Running hook script 'zz-proxmox-boot'..
   Re-executing '/etc/kernel/postinst.d/zz-proxmox-boot' in new private mount namespace..
   No /etc/kernel/proxmox-boot-uuids found, skipping ESP sync.
   Couldn't find EFI system partition. It is recommended to mount it to /boot or /efi.
   Alternatively, use --esp-path= to specify path to mount point.
   ```

   > GPT-4 的解答中的有用部分
   >
   > 从你的错误信息来看，系统在寻找EFI系统分区时未能找到，这可能是因为你的系统是使用BIOS方式启动的，而不是EFI方式。在BIOS启动方式下，系统不会有EFI系统分区。
   >
   > 3. 如果你的系统不需要EFI系统分区，那么你可以尝试忽略这个错误，继续你的操作。这个错误可能是由于系统在处理initramfs时，错误地认为你的系统需要EFI系统分区导致的。你可以尝试更新你的系统，或者更换其他的内核版本，看是否可以解决这个问题。
   >

4. 重启 `sudo reboot`, .... 并检查`dmesg | grep iommu ` . 这个输出显示了硬件直通已经成功开启. 因为(a) 在命令行参数中，你可以看到`intel_iommu=on`和`iommu=pt`。这两个参数分别表示开启Intel的IOMMU支持和设置IOMMU为直通模式。(b) 在输出的后半部分，你可以看到很多PCI设备被添加到了不同的IOMMU组中。这表示这些设备已经被IOMMU管理，可以被直接分配给虚拟机使用。

   所以，这个输出显示了硬件直通已经成功开启。

   ```bash
   [    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-6.5.11-7-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on iommu=pt video=vesafb:off video=efifb:off video=simplefb:off
   [    0.104754] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-6.5.11-7-pve root=/dev/mapper/pve-root ro quiet intel_iommu=on iommu=pt video=vesafb:off video=efifb:off video=simplefb:off
   [    0.532387] iommu: Default domain type: Passthrough (set via kernel command line)
   [    0.611885] pci 0000:00:1b.0: Adding to iommu group 0
   [    0.611997] pci 0000:ff:0b.0: Adding to iommu group 1
   [    0.612019] pci 0000:ff:0b.1: Adding to iommu group 1
   ....
   [    0.615079] pci 0000:04:00.0: Adding to iommu group 45
   [    0.615101] pci 0000:06:00.0: Adding to iommu group 46
   ```

### 开启硬盘直通

虚拟机添加 PCIE 设备 SATA 控制器。开机后就会自动占用，接着 pve 主节点就会丢失对 硬盘的控制

![image-20231215145820838](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231215145820838.png)



### [开启显卡直通](https://foxi.buduanwang.vip/virtualization/pve/561.html/)

1. 将 Linux 对显卡的默认驱动列入黑名单，以防止它们在启动时加载。这样，当你配置显卡直通时，虚拟机可以直接访问显卡，而不会与主机操作系统的驱动程序冲突，本机采用的显卡是非公版的  gtx 1050 Ti 显卡, 但仍是按照 Nvidia 的标准，因此选择 Nvidia 的屏蔽方法
   ```bash
   #直通NVIDIA显卡，请使用下面命令
   ❯ echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf 
   ❯ echo "blacklist nvidia" >> /etc/modprobe.d/blacklist.conf 
   ❯ echo "blacklist nvidiafb" >> /etc/modprobe.d/blacklist.conf 
   ```

   1.  找到显卡的信息并添加到 `/etc/modprobe.d/vifo.conf` 中

   ```bash
   # 记下 GPU 编号
   ❯ gpu_number=$(lspci -nn | \grep -i vga | awk -F '.' '{print $1}')
   ❯ echo $gpu_number
   03:00
   
   # 记下 GPU 设备 ID, 包含一个声卡，一个显卡
   ❯ pcie_devices_ids=$(lspci -n -s "$gpu_number" |  awk '{print $3}' | paste -sd ',')
   ❯ echo $pcie_devices_ids
   10de:1c81,10de:0fb9
   
   将设备ID添加到 vfio.conf
   ❯ echo "options vfio-pci ids=$pcie_devices_ids"  > /etc/modprobe.d/vfio.conf
   ❯ cat /etc/modprobe.d/vfio.conf
   options vfio-pci ids=10de:1c81,10de:0fb9
   ```

2. 其他参数

   ```bash 
   # 允许不安全的中断
   ❯ echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf
   
   # 忽略异常，防止虚拟机异常导致宿主机崩溃
   #   ignore_msrs             :   忽略异常
   #   report_ignored_msrs     :   是否报告异常
   ❯ echo "options kvm ignore_msrs=1 report_ignored_msrs=0" > /etc/modprobe.d/kvm.conf
   ```

3. 应用更新 并重启。
   ```bash
   # 应用更新 并重启
   ❯ update-grub
   ❯ update-initramfs -k all -u
   ❯ reboot
   ```

4. 检查是否应用成功。如果看到`Kernel driver in use: vfio-pci`，表示应用成功

   ```bash
   # 检查是否应用成功
   ❯ lspci -nnk | \grep -i --color  nvidia -A 3
   03:00.0 VGA compatible controller [0300]: NVIDIA Corporation GP107 [GeForce GTX 1050] [10de:1c81] (rev a1)
           Subsystem: GALAX GP107 [GeForce GTX 1050] [1b4c:11c0]
           Kernel driver in use: vfio-pci
           Kernel modules: nvidiafb, nouveau
   03:00.1 Audio device [0403]: NVIDIA Corporation GP107GL High Definition Audio Controller [10de:0fb9] (rev a1)
           Subsystem: GALAX GP107GL High Definition Audio Controller [1b4c:11c0]
           Kernel driver in use: vfio-pci
           Kernel modules: snd_hda_intel
   ```

   

5. 在创建以后的虚拟机的硬件界面，添加 PCIE 显卡设备。如下图所示。

   ![image-20231215000827967](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231215000827967.png)

   ![image-20231215001023243](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231215001023243.png)

   ![image-20231215000949226](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231215000949226.png)

6. 启动虚拟机后，如果是 Windows 虚拟机，则查看 设备管理器中已经识别到 Nvidia 显卡，如果识别不到，需要安装驱动  仓库 [virtio-win/virtio-win-pkg-scripts](https://github.com/virtio-win/virtio-win-pkg-scripts?tab=readme-ov-file) 中的驱动，在 Windows11 虚拟机中下载文件 [virtio-win-0.1.240.iso](https://github.com/virtio-win/virtio-win-pkg-scripts?tab=readme-ov-file) ，加载 iso 文件安装里面的 `virtio-win-gt-x64.msi` 文件，重启即可看到 Nvidia 设备。

   ![image-20231215001323399](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231215001323399.png)

7. 虚拟机硬件中的虚拟 vga 会跟 Nvidia 设备冲突，Windows 会自动关闭 Nvidia 设备，因此需要在硬件中关闭 虚拟 vga 后重启，即可在设备管理器和任务管理器的性能页面看到 GPU 了，此后再重新加回 虚拟 VGA 也能继续看到。

   > 在PVE（Proxmox VE）中关闭虚拟VGA的步骤如下：
   >
   > 1. 登录到Proxmox VE的管理界面。
   >
   > 2. 在左侧的服务器浏览器中，选择你想要修改的虚拟机。
   >
   > 3. 在虚拟机的配置页面中，找到“硬件”选项卡。
   >
   > 4. 在硬件列表中，找到“显示设备”，点击它。
   >
   > 5. 在弹出的窗口中，你可以看到一个下拉菜单，其中列出了所有可用的显示设备。选择“无”或者“none”。
   >
   > 6. 点击“确定”保存你的设置。
   >
   > 7. 重启虚拟机，让新的设置生效。
   >
   > 这样，虚拟VGA就被关闭了。请注意，关闭虚拟VGA后，你可能无法通过Proxmox VE的控制台访问虚拟机的图形界面。你需要通过其他方式（如SSH或者RDP）来访问虚拟机。

7. 硬件直通后，蓝屏不断，我投降了，看看咋解决 [win10](https://post.smzdm.com/p/avx8pn3n/) 试试。

### VGPU 虚拟化

以下实验参考 [[vGPU在Proxmox VE下的配置与使用](https://xinalin.com/159/vgpu-configuration-on-pve)] 和 **GPT4**

由于使用 nvidia rtx 1050 反复失败，所以决定买一张功耗价钱都一样，但算力是三倍的二手专业 GPU, 可能是矿卡. 不过用来提供虚拟机的远程桌面渲染或者作为 nas 的实时转码还是够用的。

除了该原因以外，GPU 的虚拟化也更符合最开始的目标，榨干硬件资源。

> NVIDIA GeForce RTX 1050 和 Tesla P4 是两款不同的图形处理器（GPU），它们的算力和 CUDA 核心数如下：
>
> 1. NVIDIA GeForce RTX 1050：这款 GPU 主要面向消费级市场，适用于一般的图形渲染和游戏。它的 CUDA 核心数为 640 个。在 FP32 浮点运算中，其理论峰值性能为 1.86 TFLOPS。
>
> 2. NVIDIA Tesla P4：这款 GPU 面向数据中心和企业级市场，适用于深度学习和人工智能应用。它的 CUDA 核心数为 2560 个。在 FP32 浮点运算中，其理论峰值性能为 5.5 TFLOPS。
>
> 请注意，虽然 CUDA 核心数和算力可以提供一些关于 GPU 性能的信息，但它们并不能完全决定 GPU 的性能。GPU 的性能还受到其他因素的影响，如内存带宽、内存容量、驱动优化等。

1. 步骤类似显卡直通

```bash
# 把下面几行追加到 /etc/modules 里，用于加载所需的内核模块
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
 
# 屏蔽开源驱动
echo "blacklist nouveau" >> /etc/modprobe.d/pve-blacklist.conf

# 当虚拟机试图访问 MSR 时，KVM 会主动触发系统崩溃 [11]。需要通过配置文件忽略 MSR 并抑制日志。
echo "options kvm ignore_msrs=1 report_ignored_msrs=0" > /etc/modprobe.d/kvm.conf

 
update-initramfs -k all -u
reboot
 
# 开启IOMMU，参考：https://foxi.buduanwang.vip/yj/561.html
## AMD主板可能需要修改为：quiet amd_iommu=on iommu=pt pcie_acs_override=downstream，否则部分PCI设备可能会在同一个分组内，直通时会直通整个设备
 
# 安装依赖包
apt install build-essential dkms mdevctl pve-headers-$(uname -r)
 
# 重启机器
reboot
```

2. 在 PVE 上安装 Host-Driver/nvidia 驱动，这回是专业卡，过程较为丝滑。

   ```bash
   > ./NVIDIA-Linux-x86_64-535.129.03-vgpu-kvm.run
   Verifying archive integrity... OK
   Uncompressing NVIDIA Accelerated Graphics Driver for Linux-x86_64 535.129.03..
   ❯ dkms status
   nvidia/535.129.03, 6.5.11-7-pve, x86_64: installed
   ❯  nvidia-smi
   Tue Dec 19 17:30:33 2023
   +---------------------------------------------------------------------------------------+
   | NVIDIA-SMI 535.129.03             Driver Version: 535.129.03   CUDA Version: N/A      |
   |-----------------------------------------+----------------------+----------------------+
   | GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
   | Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
   |                                         |                      |               MIG M. |
   |=========================================+======================+======================|
   |   0  Tesla P4                       Off | 00000000:03:00.0 Off |                  Off |
   | N/A   34C    P0              23W /  75W |     32MiB /  8192MiB |      0%      Default |
   |                                         |                      |                  N/A |
   +-----------------------------------------+----------------------+----------------------+
   
   +---------------------------------------------------------------------------------------+
   | Processes:                                                                            |
   |  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
   |        ID   ID                                                             Usage      |
   |=======================================================================================|
   |  No running processes found                                                           |
   +---------------------------------------------------------------------------------------+
   
   ```

3. 在虚拟机上安装 Guest-Driver 驱动 `./xxxx.run`， Windows 打开 exe 文件一直点就行，安装成功后通过 `nvidia-smi` 查看安装成功与否，理论上输出的信息于 PVE 输出的形式差不多。

4. 搭建授权服务器: 在一个节点上，进入 root 用户后，执行如下命令

   ```bash
   WORKING_DIR=/opt/docker/fastapi-dls/cert
   mkdir -p $WORKING_DIR
   cd $WORKING_DIR
   openssl genrsa -out $WORKING_DIR/instance.private.pem 2048 
   openssl rsa -in $WORKING_DIR/instance.private.pem -outform PEM -pubout -out $WORKING_DIR/instance.public.pem
   openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout  $WORKING_DIR/webserver.key -out $WORKING_DIR/webserver.crt
   ```

   在喜欢的目录下创建 `docker-compose.yml` 文件并加入如下内容，注意修改 其中的 `DLS_URL` 变量为该主机的 IP,  接着执行  `docker compose up -d` 即可启动容器

   ```bash
   version: '3.9'
    
   x-dls-variables: &dls-variables
     TZ: Asia/Shanghai 
     DLS_URL: localhost # 修改此处为此DLS服务器的IP或域名 
     DLS_PORT: 443
     LEASE_EXPIRE_DAYS: 1800  # 证书有效期默认为90天
     DATABASE: sqlite:////app/database/db.sqlite
     DEBUG: false
    
   services:
     dls:
       image: collinwebdesigns/fastapi-dls:latest
       restart: always
       environment:
         <<: *dls-variables
       ports:
         - "443:443" # 内部端口固定为443。要修改DLS的端口，同步修改冒号前的值与DLS_PORT的值
       volumes:
         - /opt/docker/fastapi-dls/cert:/app/cert
         - dls-db:/app/database
       logging:
         driver: "json-file"
         options:
           max-file: 5
           max-size: 10m
    
   volumes:
     dls-db:
   
   ```

   通过如下命令测试，授权服务的启动是否正常

   ```bash
   curl.exe --insecure -L -X GET https://DLS_URL:DLS_PORT/-/client-token 
   ```

5. Windows 虚拟机授权: 使用管理员打开 Powershell, 执行如下命令，如无意外可成功授权

   ```powershell
   curl.exe --insecure -L -X GET https://DLS_URL:DLS_PORT/-/client-token -o "C:\Program Files\NVIDIA Corporation\vGPU Licensing\ClientConfigToken\client_configuration_token_$($(Get-Date).tostring('dd-MM-yy-hh-mm-ss')).tok"
    
   Restart-Service NVDisplay.ContainerLocalSystem
   
   nvidia-smi.exe -q | Select-String License
   
       vGPU Software Licensed Product
           License Status                    : Licensed (Expiry: 2028-11-23 9:52:18 GMT)
   
   ```

疑难解答：

1. 在此过程中，我曾经并没有执行该命令 `echo "options kvm ignore_msrs=1 report_ignored_msrs=0" > /etc/modprobe.d/kvm.conf`, 而导致无法授权成功

### NAS 软件系统构建实验

简单来说 NAS 可以理解成 私人网盘，但关键在于 其私服的特性，保证安全、容量，其容量和速度取决于个人的投资，在该 NAS 基础上构筑的应用依赖于生态如何，取决于个人能力。

毫无疑问，它的使用场景可以是

1. 家庭媒体中心
2. 敏感个人数据存储；
3. 数据冷备
4. 局域网内挂载盘等等

> NAS（Network Attached Storage）是指网络附加存储，是一种通过网络连接的存储设备。它可以提供高容量的数据存储，同时可以通过局域网或互联网实现多设备间的共享和访问。

既然是通过网络使用存储，就不拘泥于各种各样的名词了，如 nfs, nas, ftp 等各种各样的协议。

要是足够简单的，在 Windows 平台下，使用 alist + 网盘 + raidrive 就能使用多个网盘的大容量服务。

但这只解决了存储层，没有构建上层应用。此外相对于私服的 公共服务，有其优缺点：

优点显然是：

1. 付费会员的服务稳定、高速；
2. 共享资源丰富
3. 上层应用能利用网盘的连接能力，生态越丰富，生产效率越高。

缺点是：

1. 数据有审查机制，包含 机器扫描 等等。
2. 互联网公司内部有对开发人员的鉴权等数据隐私保护的机制，但是很显然没有绝对的安全，对于部分人群，或者部分敏感数据，可能并不合适。
3. 应用限制于网盘提供的服务，不开放。
4. 普通用户与狗不得进入，订阅开资。

此外 对于于动手能力强、热衷 DIY 的人，自己建造也是个良好的实践路径。有何良好的解决方案呢。比较热门的商业方案有 群晖，开源方案有 TrueNas, 更多的可以参考 [awesome-self-hosted-services](https://awesome-selfhosted.net/#contributing). 

本文是个人旨在根据需求，进行实验，对比不同解决方案的优缺点。在使用软件服务的使用，请尊重创作者的劳动成果、尊重版权。

首先针对群晖 NAS 的方案进行实验。

1. 实验目的：探讨群晖 NAS 解决方案的优缺点；
2. 实验平台：
   1. 硬件平台: CPU Intel Xeon E5 2690V4 12cores 24 threads; GTX 1050 Ti 2G; 内存: 48G 三星 服务器内存条；1个 512G固态，2个 3T 机械硬盘；
   2. 软件平台：PVE 虚拟化管理平台。
3. 实验设计：搭建群晖系统，测试如下几个场景的使用体验：
   1. 多机器在同一局域网内的网络读写性能，如带宽、延迟
   2. 将存储作为挂载盘使用的性能
   3. 基于存储盘搭建的媒体中心的使用。

在实验开始前，简述过程。群晖是系统软件，就是说安装它如同 Windows 和 Ubuntu 一样都需要引导和系统镜像，而且它是商业软件，因此引导是要付费的，黑群晖指采用[社区提供的引导镜像](https://github.com/fbelavenuto/arpl) 或者它的[汉化版](https://github.com/wjz304/arpl-zh_CN/releases)。大概可以简单将[引导镜像理解为 主板的 BIOS](https://www.chiphell.com/thread-2497442-1-1.html), 所以首先需要在 PVE 里创建虚拟机，将引导镜像作为系统镜像安装，安装结束后进入群晖的页面再正式安装群晖，这样安装好的群晖系统是功能受到限制的实验型系统，进一步授权需要购买序列号。

实验步骤：

1. 准备：

   1. 系统版本选择：[家庭偏重于媒体中心的选用 DS920+](https://www.chiphell.com/thread-2497442-1-1.html) 

   ![image-20231214173327356](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231214173327356.png)

   1. 下载社区提供的汉化镜像：wjz304/arpl-zh_CN 仓库里的 [arpl-zh_CN-1.1-beta2a.img.zip](https://github.com/wjz304/arpl-zh_CN/releases/tag/v1.1-beta2a) , 上面编译好的引导镜像有 `img.zip`, `vmdk*` 说明也可以通过 直接开 vmware 虚拟机；
   2. 下载 [系统镜像](https://archive.synology.com/download/Os/DSM)， 选择  [7.1.1-42962 (with Update 1)](https://archive.synology.com/download/Os/DSM/7.1.1-42962-1-NanoPacked) ， 也可以不下载，等到安装时直接访问群晖官网下载；选择 7.1 的原因是：越新越好，但是 7.2 的会在后续出现 `configuration not found`  的情况。
   3. 下载[群晖助手 Windows exe](https://www.suncan.com.cn/archives/5612), 用于引导成功后寻找局域网内的群晖服务器进一步安装。
   4. 参考[教程](https://www.52pojie.cn/thread-1700365-1-1.html) 将 引导镜像上传到 PVE. 
   5. （可选）开启硬盘直通和显卡直通。

2. 参考[教程](https://www.52pojie.cn/thread-1700365-1-1.html) 创建虚拟机：CPU, 内存, 硬盘 照着填。

3. 制作并更换虚拟机的系统盘：

   1. 分离并移除 sata0 硬盘（系统盘）

   2. 执行命令制作虚拟机 `100` 的系统引导盘, 命令 格式为

       `qm importdisk vm-id /path/to/your/image/file storage-pool`

      例如我的命令是

      `qm importdisk 100 /var/lib/vz/template/iso/arpl.img local-lvm`

      看到命令行显示 `sucessfully imported disk as ...` 即成功

   3. 给虚拟机分配硬件：添加硬盘、PCI 设备，如 sata controller, 显卡等等。可参考 [pve安装群晖教程](https://www.52pojie.cn/thread-1700365-1-1.html), 和 [pve硬件直通](https://foxi.buduanwang.vip/virtualization/1754.html/)

   4. 启动虚拟机，能在控制台看到其 IP:Port 的字样，即引导镜像的配置网址。

   5. 依据[教程](https://www.chiphell.com/thread-2497442-1-1.html) 配置好 引导，其中关键有 选择型号(choose a model)为 DS920+   ==>>  选择构建版本(choose a build number)为最新的   ==>> 添加 CPU detail 插件   ==>> SN 号为授权序列号，可自己购买后输入，否则生成随机的 , 如果购买了 序列号也要输入 MAC 地址  ==>> 选择 Build the loader, 即会自动编译，过会就好 ==>> 启动(Boot the loader)，此时在控制台能看到系统重启；

   6. 系统重启后在控制台能看到 IP, 在浏览器输入  http://IP:5000 即可进入群晖的初始化页面，或者使用 准备步骤中安装好的 群晖发现助手。登录后按提示点点点即可，其中有一步时安装群晖系统，会提示选择本地下载号的 pat 结尾的文件（第一步准备号的），或者直接选择从官网下载。

      1. 装好重启能进入了，再进行系统管理方面的配置。	

   

以下错误是安装 DSM 7.2 遇到的错误，安装 7.1 即可

![image-20231214112818501](D:\OneDrive\0001Surface\notes-sync-to-github\awesome-self-hosted-services\assets\image-20231214112818501.png)

### 使用 PVE 搭建 Kubernetes 实验集群

参考该[文章](https://phyng.com/2023/04/09/pve-kubernetes.html)搭建集群, 文章非常详细，在此不再详述



### 构建 Windows 11 作为多人使用的云电脑/云游戏机

现如今手机 5G 普及，带宽和延迟上了一个层次，家庭宽带也在提速，ipv6 公网 IP 也能分配到，终端设备解码能力强，更加创造了一个非常友好的环境给我们互联设备，体验云游戏、云桌面。

假如在一个固定的地点搭建了个云电脑服务器，在公网的一个地方通过手机连接，带上充电宝，连接上云电脑，将可实现无处不在的长续航云办公、云游戏。

本小节主要简述在服务器上搭建个人云电脑的实验和优化。

实验平台：

1. CPU: Intel(R) Xeon(R) CPU E5-2690 v4 @ 2.60GHz 14 核 28 线程
2. GPU: 无
3. 内存: 48GB
4. 硬盘：致钛固态
5. 系统：windows11专业版

首先参考网上 [教程](https://www.gordon2000.com/2021/10/pvewindows-11-step-by-step.html) 安装 Windows11，接着考虑远程桌面解决方案，横向对比主要从以下几个方面考虑

1. 网络优化：带宽、延迟；
2. 帧率优化, 提升帧率至 60fps；
3. RDP 协议配置优化。
4. GPU 硬件加速；
5. 共享 USB 等外设。

远程桌面方案：

| 解决方案       | 延迟 | 画质                       | 外设共享 | 剪贴板| 网络要求|硬件要求| 场景  |
| -------------- | ---- | -------------------------- | -------- | ---------- | ---- | ---| --- |
| 微软自带 RDP   | 低   | 播放高帧高码率视频会有卡顿 | 完美     | 完美       | 低，P2P | 低 | 日常办公 |
| todesk         | 低 | 低 | 未测试 | 应该可以 | 低，无需公网IP |低|远程指导|
| parsec         | 低 | 高 | 未测试 | 未测试 | 高(最高 50mpbs 上传，低延迟)<br />P2P |高(其实也不高，现在的个人PC哪个不支持)|丝滑办公；游戏；播放4k60fps <br />youtube 4k 60fps 双子杀手完美|
| pve 自带显示器 | 低   | 低                         | 少       | 无 | 低 |低|初始化配置|

Parsec 配置非常简单，一直点点点就是了，然后进去 Host 和 Client 按需配置。在 PVE 虚拟机的情况下，如果监视器不关掉的话，windows 会显示有两个显示屏。

RDP 的配置则参考 reddit 上在疫情期间讨论远程办公如何榨干硬件性能的帖子: [Pushing Remote FX to its limits](https://www.reddit.com/r/sysadmin/comments/fv7d12/pushing_remote_fx_to_its_limits/).

目前美中不足的是 parsec 好像没法与 RDP 共存，究竟是什么冲突了呢？

这里贴以下原文的翻译(稍微改一下排版，GPT4 提供翻译服务)：

> 计算机配置 > 策略 > 管理模板 > Windows 组件 > 远程桌面服务 > 远程桌面会话主机 > 连接
>
> 选择 RDP 传输协议 = 已启用
>设置传输类型为："使用 UDP 和 TCP"
> 
>计算机配置 > 策略 > 管理模板 > Windows 组件 > 远程桌面服务 > 远程桌面会话主机 > 远程会话环境
> 
>对所有远程桌面服务会话使用硬件图形适配器 = 已启用
> 
>优先考虑 H.264/AVC 444 图形模式用于远程桌面连接 = 已启用
> 
>为远程桌面连接配置 H.264/AVC 硬件编码 = 已启用
> 将 "优先考虑 AVC 硬件编码" 设置为 "始终尝试"
>
> 为 Remote FX 数据配置压缩 = 已启用
>设置 RDP 压缩算法："不使用 RDP 压缩算法"
> 
>为 RemoteFX 自适应图形配置图像质量 = 已启用
> 将图像质量设置为 "高"（在 WAN 连接上，无损似乎太残酷了。）
>
> 启用 RemoteFX 编码，用于为 Windows Server 2008R2 SP1 设计的 RemoteFX 客户端 = 已启用。
>
> 计算机配置 > 策略 > 管理模板 > Windows 组件 > 远程桌面服务 > 远程桌面会话主机 > 远程会话环境 > Windows Server 2008R2 的 Remote FX
>
> 配置 Remote FX = 已启用
>
> 优化使用 Remote FX 时的视觉体验 = 已启用
>设置屏幕捕获率（每秒帧数）= 最高（最佳质量）
> 设置屏幕图像质量 = 最高（最佳质量）
>
> 优化远程桌面会话的视觉体验 = 已启用
>设置视觉体验 = 丰富的多媒体
> 
>--------------------------结束--------------------------
> 
>应用以下注册表设置以进一步优化 RemoteFX：
> 
>;---------------------TurboRemoteFXHost.reg---------------------
> 
> Windows 注册表编辑器版本 5.00
>
> ;设置 RDP 的 60 FPS 限制。
>;来源：https://support.microsoft.com/en-us/help/2885213/frame-rate-is-limited-to-30-fps-in-windows-8-and-windows-server-2012-r
> 
>[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations]
> 
>"DWMFRAMEINTERVAL"=dword:0000000f
> 
> ;提高 Windows 响应性
>;来源：https://www.reddit.com/r/killerinstinct/comments/4fcdhy/an_excellent_guide_to_optimizing_your_windows_10/
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile]
>
> "SystemResponsiveness"=dword:00000000
> 
>;设置显示与通道带宽（即RemoteFX设备，包括控制器）的流控制。
> 
>[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\TermDD]
> 
>"FlowControlDisable"=dword:00000001
> 
>"FlowControlDisplayBandwidth"=dword:0000010
> 
> "FlowControlChannelBandwidth"=dword:0000090
> 
>"FlowControlChargePostCompression"=dword:00000000
> 
> ;移除RDP的人为延迟。
>
> [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
>
> "InteractiveDelay"=dword:00000000
>
> ;禁用Windows网络节流。
>
> [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters]
>
> "DisableBandwidthThrottling"=dword:00000001
> 
>;启用大MTU数据包。
> 
>"DisableLargeMtu"=dword:00000000
> 
>;禁用WDDM驱动程序并返回到传统的XDDM驱动程序。（对于Nvidia显卡来说，性能更好，对于AMD显卡，你可能需要更改此设置。）
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services]
>
> "fEnableWddmDriver"=dword:00000000
>
> ;----------------主机注册表设置结束----------------
>
> ----------------客户端更改----------------
>
> 为了通过游戏控制器，客户端需要Windows 7/8/8.1/10 Pro（不是家庭版）才能使RemoteFX USB设备重定向工作。
>
> 用户还需要在他们的家庭PC上进行以下更改。
>
> （摘自我们的用户指南）
>
> 在家庭PC上（你正在连接的计算机...）
>
> 按Windows键+R打开运行对话框
>
> 然后输入gpedit.msc并点击确定
>
> 导航到：
>
> 本地计算机策略 > 计算机配置 > 管理模板 > Windows 组件 > 远程桌面服务 > 远程桌面连接客户端 > RemoteFX USB设备重定向
>
> 在右侧双击“允许RDP重定向其他受支持的RemoteFX USB设备从此计算机”
>
> 选择已启用的单选按钮，然后点击下拉菜单从"管理员"更改为"用户和管理员"
>
> 点击应用。
>
> 然后按Windows键+R再次打开运行对话框并运行：“gpupdate /force”并再次重启你的PC。
>
> 这应该允许你现在启用USB控制器的USB透传。
>
> ----------------客户端更改结束----------------
>
> 

在优化后目前体验非常棒，但是高码高帧率视频和远程游戏还没测试。

测试方向可能包括但不止以下几项

- [ ] 轻负载：网页浏览、开普通 office 应用
- [ ] 高负载办公场景：开很多个应用，高强度工作。
- [ ] 重图形渲染负载：打游戏、看高码率视频。
- [ ] 开多个显示器：在 RDP 客户端，导航到 "显示" => "显示配置" => 勾选 "将我的所有监视器用于远程会话"
- [ ] 外设共享。在设备管理器能看到远程设备，包括 usb, 显示器等等。

实验结果

1. 轻负载：没问题，体验棒
2. 重图形渲染负载：看 youtube 双子杀手明显丢帧，没有60fps的流畅感，但是 `https://www.testufo.com/` 网站并没有显示出丢帧，14 个 CPU 一半的利用率

## 以双路 epyc 7542 为 CPU 的 多人云电脑 Hub / 工作站

epyc 7542 单核主频 2.9Ghz 用作云电脑，非常不错，可以看到有[天翼云电脑的业界案例](https://www.cnii.com.cn/rmydb/202202/t20220228_360762.html)，自己搭建也没问题。

场景不单纯用作云电脑服务器，还可以进一步升级 GPU, 变为 深度学习服务器、远程游戏服务器 和 高性能工作站，同时承担 24 小时 online 的服务也不在话下。成本虽然突破万元，但是有8个人用四五年也足够便宜。

场景：

1. 工作室内部多人共用；
2. 实验室内部多人公用；
3. 家庭共用
4. 工作站。

缺点：

1. SOP 单点故障，要是多个用户正在使用该电脑处理重要事情，假如出问题了咋解决。内部玩、工作室内部能及时处理可以，但是要是比较分散感觉还是不一定行得通。解决方法是：双路 epyc7542 + 独立 NAS + 独立高性能游戏机(或者可以直接采用 13899K)。NAS 存储数据(同时备份副本到 115, 阿里, Onedrive 等多个网盘)，游戏机同时作为游戏机。其实设备可以是二手的，做好 故障转移和备份就行。
   1. 内存的故障转移技术了解一下：对于内存条的故障，一种可能的解决方案是使用支持ECC（Error Checking and Correction）的内存和主板。ECC内存可以自动检测并修复内存中的错误，从而提高系统的稳定性和可靠性。然而，ECC内存通常比普通内存更贵，而且需要主板的支持
   2. 如果是工作室内部使用的话，故障啥的都好讲。网络啥的不成问题，容易解决。

[参考 B 站 瓜皮up主的硬件组合](https://www.bilibili.com/video/BV1vH4y1D7ow/)，暂时仅列出大头项目

| 组件 | 品牌及其型号                   | 价格              | 参数           |      |
| ---- | ------------------------------ | ----------------- | -------------- | ---- |
| 主板 | 超微 h11ssl                    | 2200              |                |      |
| CPU  | Epyc 7542 * 2                  | 5300              | 单核性能2.9Ghz |      |
| GPU  | Rtx 1080 Ti 8G /Rtx 3090Ti 24G | 2000 ~ 8000 ~ 1W+ |                |      |
| 内存 | 三星 DDR4 2133 16G * 8         | 720               |                |      |
| 硬盘 | 4 * 800G Intel SSD + 4 * 2T    | 1680              |                |      |
| 其余 |                                | 1000              |                |      |
|      |                                |                   |                |      |
|      |                                |                   |                |      |
| 总价 |                                | 12900 ~ 2W        |                |      |



### 围绕 epyc 7d12 低功耗 多核 CPU 搭建的 NAS 

待续

## 个人远程游戏主机

[论坛有人提到](https://www.chiphell.com/forum.php?mod=viewthread&tid=2535965&highlight=%E6%B8%B8%E6%88%8F&mobile=no)，epyc CPU 并不适合打游戏，因此需要搭建台桌面级游戏主机。

[EVE Online](https://sysrqmts.com/zh/games/eve-online) 的推荐配置为 [Intel Core i7-7700](https://sysrqmts.com/cpus/intel/core-i7/kaby-lake/7700) 四核八线程 3.6 GHz~4.2 GHz 频率，而 12900K 是 16核 24线程 8性能核+8能效核，性能核3.2~5.2Ghz, 能效核2.40~3.9Ghz，再配上 rtx 3060 8G GPU, 应该支持两个人同时玩没问题。



> 因为EPYC的外围设备多（支持128条PCI-E），CPU内部PCI-E控制器和内存控制器之间交换数据会比较复杂。
>
> 显卡运行效率大幅降低，游戏性能会受很大拖累，导致还不如普通桌面CPU

