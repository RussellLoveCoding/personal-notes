# Networking

本文主要探讨再部署私服过程中遇到的网络问题以及解决方法。



## 链式代理/代理转发

假设某个爬虫项目里，执行任务的节点S1 在网络分区 A, 代理服务器节点S2在网络分区 B, 代理池服务器S3在网络分区 C, 目标网站S4在 分区 D,  AB 可连通，AC, AD 不可连通，BC, BD, CD 可连通。

此时节点S1 需要链式代理/代理转发来实现在分区 A 通过两层的代理 -> S2(B) -> S3(C) 来最终访问 S4(D), S2(B) 负责解决连通性问题，S3(C) 负责提供多个 IP.

具体而言在国内爬国外的网站就符合上述的模型，而 [v2ray/xray](https://toutyrater.github.io/advanced/outboundproxy.html) 可满足该需求。

## 代理

假设主机1 在网络分区 A, 主机1 需要与 网络分区 B 的节点通信，此时需要代理方案。在中国大陆的主机旧属于这种情形，需要代理，否则在中国大陆进行开发所涉及到不同的软件源在国外速度比较好，但是国内却异常感人。

本小节的**目标**是 本地开发环境的网络应该具有透明代理，使得能够无缝开展开发工作，而非手动修改一大堆软件源，因为后者过于麻烦。注意这里主要针对的是常规的工作学习需要。对利用本文的方法实施 包括正常工作学习的目的 和 其他目的 的一切行为不负责，请遵守当地法律。

本实验的配置如下

- **主机**：单台 Windows 电脑，WSL2 作为虚拟开发机，网络采用最新的镜像模式，同时防火墙也复制使用 windows 的防火墙

- **网络**：支持 ipv4 和 ipv6 双栈，运营商为联通;
- vps: 支持双栈网络的香港节点;
- 加密通讯协议：[projectx](https://xtls.github.io/) 的 xray-reality.

测试出国线路：

首先测试一下不代理的情况下，对一些目标网站访问的去程路由，接着利用免费试用的机会，测试主流云计算厂商作为代理 vps 对国内网络的线路优化程度(包括去程路由和回程路由，本文只给出去程路由，回程同理，或者使用[脚本 autotrace.sh](https://github.com/Chennhaoo/Shell_Bash?files=1)测试回程到三网的路由, 更多测试脚本见 相关 vps 论坛，如 [1024的测试vps或独服的各种脚本集合](https://1024.day/d/35))，也可参考 [v2ray-agent](https://www.v2ray-agent.com/categories/vps) 的选购指南。注意，这里的路由实验结果只保留路由过程中在城市级别或省份级别的行政区划中的最后一跳，同时隐去隐私 ip 信息。

使用 [nexttrace](https://github.com/nxtrace/NTrace-core) 测试线路去程路由得到的结果摘要如下，具体的见下文贴出的实验结果：

- 目标为 `azure-hk`: AS17816 广东联通(take 10ms)-上海联通(take50ms)-日本东京(take 30+ms)-中国香港(take10+ms)
- 目标为 `digitalocean-singapore` 广东联通-美国加州-新加坡
- 目标为 `gcp-hk` 广东联通-香港
- 目标为 `aws-singapore`: 广东(take 20ms) -> 新加坡联通(take 30ms) 
- 目标为aws/azure 其他亚洲节点的均绕路。

目标为 `api.githubcopilot.com` 的去程路由：

```bash
$ sudo nexxtrace api.githubcopilot.com # 选择域名解析到的第一个 IP 地址
9   xxx.xxx.xxx.xx  AS4837   [CU169-BACKBONE] 中国 广东省 广州市  chinaunicom.cn  联通
                                              13.15 ms / 16.11 ms / 11.51 ms
10  219.158.17.86   AS4837   [CU169-BACKBONE] 美国 加利福尼亚州 洛杉矶  chinaunicom.cn  联通
                                              241.77 ms / 238.97 ms / 237.96 ms
11  199.229.231.213 AS3257   [TINET]          美国 加利福尼亚州 洛杉矶  gtt.net
                                              188.28 ms / 188.46 ms / 188.33 ms
12  213.200.115.178 AS3257   [GTT-EU]         美国 华盛顿哥伦比亚特区 华盛顿  gtt.net
                                              250.66 ms / 257.14 ms / 266.17 ms
13  69.174.5.178    AS3257                    美国 弗吉尼亚州 郡戴维森  gtt.net

19  140.82.113.22   AS36459  [GITHU]          美国 弗吉尼亚州 杜勒斯  github.com
                                              269.80 ms / 264.59 ms / 257.50 ms
```

目标为 `raw.githubusercontent.com` 的去程路由

```bash	
$ sudo nexxtrace raw.githubusercontent.com
7   x.x.x.x         AS17816  [UNICOM-GD]        中国 广东省 XX市  chinaunicom.cn  联通
                                                10.37 ms / 10.62 ms / 9.90 ms
8   *
9   219.158.5.158   AS4837   [CU169-BACKBONE]   中国 北京市   chinaunicom.cn  联通
                                                48.45 ms / 43.88 ms / 47.31 ms
13  129.250.6.35    AS2914   [NTT-BACKBONE]     日本 东京都 东京  ntt.net
                                                108.33 ms / 106.93 ms / * ms
15  185.199.110.133 AS54113  [US-GITHUB]        ANYCAST ANYCAST  CDN fastly.com
                                                73.89 ms / 73.21 ms / 73.29 m
```

目标为 `github.dev` 的去程路由

```bash
$ sudo nextttrace github.dev
11  xxx.xxx.xx.xxx  AS4837   [CU169-BACKBONE] 中国 广东省 广州市  chinaunicom.cn  联通
                                              44.46 ms / 44.35 ms / 44.29 ms
18  20.43.185.14    AS8075                    新加坡    microsoft.com
                                              42.22 ms / 42.62 ms / 43.01 ms
```

目标为 google cloud platform 香港节点 ipv6 地址 的去程路由

```bash
$ sudo nexxtrace x:x:x:x..
8   x:x:x:x      			  AS4837   中国 广东省 广州市  chinaunicom.cn 联通
                                       * ms / * ms / 216.17 ms
9   *
10  2408:8000:2:39c::1        AS4837   中国 北京市   chinaunicom.cn 联通
                                       15.92 ms / 19.85 ms / 15.60 ms
12  2600:1900:41a0:2a9c:0:b:: AS396982 中国 香港   google.com
                                       16.26 ms / 15.99 ms / 16.37 ms
11  2001:4860:1:1::2906       AS15169  美国    google.com
                                       14.86 ms / 15.95 ms / 15.54 ms
```

目标为 google cloud platform 香港节点 ipv4 地址 的去程路由

```bash
$ sudo nexxtrace x:x:x:x..
9   xxx.x.x.xx      AS17816  [UNICOM-GD]      中国 广东省 广州市  chinaunicom.cn  联通
                                              13.51 ms / 16.65 ms / 11.90 ms
12  35.220.192.52   AS396982                  中国 香港   google.com
                                              14.72 ms / 13.99 ms / 15.33 ms
```

目标为 `digitalocean-singapore`

```bash
$ sudo besttrace 188.166.205.104
AS number       region               delay                ip                  
AS4837          中国-广东-广州-联通          15.24ms               xxx.x.x.x      
AS4637          美国-加利福尼亚州-洛杉...      164.08ms             i-94.tlot-core02... 
AS4637          新加坡-telstra....      353.41ms             unknown.telstrag...   
AS14061         新加坡-digitalo...      361.99ms             188.166.205.104   
```

等等



搭建 远程代理节点：

经过上一节的测试，可以选出适合自己的 vps. 接着在 vps 搭建代理服务。本文的实验方法采用 [v2ray-agnent](https://www.v2ray-agent.com/) 的 [八合一脚本](https://github.com/mack-a/v2ray-agent), 协议采用 projectX](项目的 相对先进的 reality 协议。

这里的例子如下：

1. 初始通过 ssh 登入 vps。并通过 `echo "your.public.key" >> ~/.ssh/authorized_keys`, `sed -rin 's~^#?\sPort 22~Port 2222~g;'`  /etc/ssh/sshd_config 的方式修改端口，同时在防火墙开放入站端口 2222(可修改为其他的)和 xray 代理流量入站端口(在下文会提到)
2. 修改密码 `sudo passwd root`
3. 切换 root 用户 `su -`
4. 执行命令 `wget -P /root -N --no-check-certificate "https://raw.githubusercontent.com/mack-a/v2ray-agent/master/install.sh" && chmod 700 /root/install.sh && /root/install.sh` 按提示执行即可。
5. 选择 5(5.REALITY管理) => 1(安装) => 域名(回车随机配置) => 端口(第一步配置到的防火墙端口) =>  public-key(回车生成新的) => uuid(回车随机)
6. 复制生成的 vless 节点 URL 备用。



本地客户端的搭建：

由于本人只有一个电脑，路由器是主路由也就是运营商提供的光猫，要实现本地开发环境的网络能够透明代理的方案有二：一是采用 xray 客户端的 UI 软件；2: 通过 hyper-v 新建一个 openwrt 作为软路由使用。第一种方法由于 Windows 与 linux 方法有异，WSL 在实现[透明代理](https://xtls.github.io/document/level-2/transparent_proxy/transparent_proxy.html) 过程中 遇到依赖缺失等错误，选择第二种方法。

参考[教程](https://www.77bx.com/212.html)通过 hyper-v 安装预安装了各种插件的 istoreOS, 其中网络接口是使用 从 hyper-v 管理器新建的外部虚拟交换机，绑定了外部物理网卡，以使得 istoreOS 桥接到主机网络。在装好 istoreOS 后连接登入，配置网络接口 如下, 接着其他设备只要在其网络配置处 将 路由、DNS 都设为 istoreOS 的 ip 就可以，这里是 `192.168.1.250` 和 `ipv6.public.ip.prefix::250/64`

```bash	
root@iStoreOS:~# cat /etc/config/network

config interface 'loopback'
        option device 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fde0:ef68:db4c::/48' 

config device
        option name 'br-lan'
        option type 'bridge'
        list ports 'eth0'

config interface 'lan'
        option proto 'static'
        option ipaddr '192.168.1.250'
        option gateway '192.168.1.1'
        option broadcast '192.168.1.255'
        option dns '192.168.1.1'
        option netmask '255.255.255.0'
        option ip6assign '60'
        option device 'br-lan'
        option ip6assign '60'
        option ip6addr 'ipv6.public.ip.prefix::250/64'
        option ip6gw 'fe80::1' # 默认网关在光猫里找到ipv6信息的默认网关
        option ip6prefix 'ipv6.public.ip.prefix::/64'
        option device 'br-lan'

config device
        option name 'br-lan'
        option type 'bridge'
        list ports 'eth0'
```



并从[此处](https://github.com/AUK9527/Are-u-ok) 下载 passwall2 的安装包到 istoreos 安装. passwall2 能够完全解锁 xray 的所有功能。具体操作在这不详述。

注意 passwall2 选择 tpproxy 并代理 tcp/udp 所有端口的流量，并自行设置好分流总结点的分流配置。

至此，透明代理的网络环境已经实现了。

