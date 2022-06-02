# 检查网卡状态

安装驱动结束后，此时网卡已经进入可工作模式。但在此之前，你需要检查网卡的状态是否正常，接着需要给网卡的端口配置IP地址，之后，网卡会进入可通信状态。

#### 检查网卡状态

实际使用过程中，你可能会遇到很多情况下网卡无法进行通信的状况，而很多情况下，导致无法网卡通信的原因则是网卡的状态并未进入工作状态。或者我这么说吧，网卡的端口是 `down` 的状态，而要让一张网卡能够通信，首先需要保证的则是网卡物理层及逻辑层链路的连通性。

安装 `InfiniBand` 驱动后，驱动包中会内置众多针对网卡的管理工具。例如，最常使用的检查网卡状态的 ibstat 工具。

```bash
#ibstat
CA 'mlx4_0'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0x0002c90300a4e720
	System image GUID: 0x0002c90300a4e723
	Port 1:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 3
		LMC: 0
		SM lid: 3
		Capability mask: 0x0251486a
		Port GUID: 0x0002c90300a4e721
		Link layer: InfiniBand
	Port 2:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 2
		LMC: 0
		SM lid: 2
		Capability mask: 0x0251486a
		Port GUID: 0x0002c90300a4e722
		Link layer: InfiniBand
```

如上，当前的网卡两个端口状态是 Active 状态，说明这张网卡状态是可以进行通信的(_当然，前提是已经对网卡的两个端口配置过IP_)。需要注意的是，这里有两个字段需要注意，其中一个是 State，该字段表示网卡端口逻辑层的状态，包含 `Active`、`Down`、`Initializating` 三种状态。

* Active: 表明从物理层到逻辑层的链路都是通的。网卡的标准状态，此时可与其他网卡进行通信
* Down: 表明逻辑层的链路当前是断开不可用的状态。此时网卡处于非工作模式状态下
* Initializating: 表明网卡的端口正处于初始化的状态。如果是短暂的从 `Initializating` --> `Active` 状态过渡`，则表明网卡的功能正常；若长时间处于 Initializating` 状态说明网卡可能存在故障。

另一个需要注意的字段则是 Physical state，该字段含义为网卡物理层的链路状态，包含 LinkUp、Pooling 两个状态。

* LinkUp: 表明网卡物理层的链路是连通的。
* Pooling: 表明网卡物理层的链路是非连通状态。通常是由于网卡没有接线所导致的。因此，遇到网卡物理层链路是 `Pooling` 状态，应该首先检查网卡的接线情况。

除此之外，还有一个工具可用来检查网卡的状态，即 `ibdev2netdev` 。

`ibdev2netdev` 会将`InfiniBand`模式的网卡形态转化为类似以太网卡的模式(_以太网卡常用 `Up` 或 `Down` 两种模式来标识网卡的状态_)。

```bash
#ibdev2netdev
mlx4_0 port 1 ==> ib0 (Up)
mlx4_0 port 2 ==> ib1 (Up)
```

使用 ibdev2netdev 往往能更加直观的看到网卡的状态，而不需要理会 `ibstat` 输出中众多的信息。



#### 配置 `InfiniBand` 网卡IP&#x20;

确认网卡状态正常后，要使 IB 网卡可以与其他网卡进行网络通信需要配置网卡的IP地址。`InfiniBand` 网卡映射到服务器上的网卡名称为 `ib{*}` 格式，这与其他以太网卡 `eno{*}` 或 `em{*}` 等格式的不同，很容器进行区分。

你可以使用 ifconfig -a | grep = 查看服务器上的 InfiniBand 网卡端口的名称

```bash
#ifconfig -a | grep =
eno1: flags=6211<UP,BROADCAST,RUNNING,SLAVE,MULTICAST>  mtu 1500
enp68s0f0: flags=4098<BROADCAST,MULTICAST>  mtu 1500
ib0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 4092
ib1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 4092
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 16436
```

获取到网卡名称后，接着需要对网卡配置IP地址。通常，服务器上的每个网口在 `/etc/sysconfig/network-scripts/` 目录下都有对应的配置文件，你可以自行修改网口的配置文件进行配置IP。(一些情况下，可能在 `/etc/sysconfig/network-scripts/` 目录下会找不到网口对应的配置文件，没关系，新建一个就可以)

```bash
#vim /etc/sysconfig/network-scripts/ifcfg-ib0
DEVICE=ib0
BOOTPROTO=static
IPADDR=192.168.2.111    
NETMASK=255.255.255.0
NETWORK=192.168.2.0
BROADCAST=192.168.2.255
ONBOOT=yes
```

网卡配置文件中各字段含义如下

* DEVICE: 设备名称。该字段后的值应该与网卡配置文件 ifcfg-<> 中的网口名称一致
* BOOTPROTO: 启动时使用的协议类型。常用的有，`static` 静态IP地址，重启网络/操作系统后IP地址不会发生变化；`dhcp` 动态IP地址分配，重启网络/操作系统后IP地址可能会发生变化；`none` 无模式，使用用户设置的IP地址。通常针对没有接线的网口进行设置
* IPADDR: 通信的IP地址。IP地址设置不能相同网段内已经存在的IP地址(避免冲突)
* NETMASK: 子网掩码。通过设置为 `<IP>/24`，也即是 `255.255.255.0`
* NETWORK: 网络地址段
* BROADCAST: 网卡广播地址。通常广播最后八位为 `255`，表现为 `*.*.*.255`
* ONBOOT: 启动时是否加载该网口。如果设置为 `no`，则重启网络服务/操作系统后，不会自动加载网口，也就是开机后网口的状态会是 `down` 状态，需要手动执行 `ifconfig <ib*> up` 使网卡正常工作；若设置为 yes，则重启网络服务/操作系统后会加载网口服务，使网口状态正常(特别注意，没有接线的网口不要设置为yes，否则会导致启动网络服务失败)。
