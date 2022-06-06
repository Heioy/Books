# ibstat

ibstat 用来查询 InfiniBand 网卡设备的基本信息，主要包含 LID(Base LID)、SM LID(本地子网ID)、Port State(端口状态)、Link Width Active(连接状态)、Physical State(物理层状态)。

ibstat 与 ibstatus 命令行展示的 InfiniBand 网卡信息非常相似，但相比 ibstatus 命令行工具，ibstat 中提供了更多的参数可以用来展示IB设备关于端口、网卡的更详细的信息。



#### 命令参数

ibstat 中提供了如下一些参数

* \-l, --list\_of\_cas: 列出节点上所有的HCA设备的名称。
* \-s, --short: 输出简要信息。
* \-p, --port\_list: 输出端口的GUID
* ca\_name: 展示指定CA的信息&#x20;
* portnum: 展示指定CA中的端口信息



#### 示例

* 查看IB设备的基本信息

```bash
#ibstat
CA 'mlx4_0'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0xec0d9a03000c8980
	System image GUID: 0xec0d9a03000c8983
	Port 1:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 10
		LMC: 0
		SM lid: 9
		Capability mask: 0x02514868
		Port GUID: 0xec0d9a03000c8981
		Link layer: InfiniBand
	Port 2:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 12
		LMC: 0
		SM lid: 9
		Capability mask: 0x02514868
		Port GUID: 0xec0d9a03000c8982
		Link layer: InfiniBand
CA 'mlx4_1'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0xf45214030033b850
	System image GUID: 0xf45214030033b853
	Port 1:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 1
		LMC: 0
		SM lid: 7
		Capability mask: 0x02514868
		Port GUID: 0xf45214030033b851
		Link layer: InfiniBand
	Port 2:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 4
		LMC: 0
		SM lid: 7
		Capability mask: 0x02514868
		Port GUID: 0xf45214030033b852
		Link layer: InfiniBand
```

输出中的关键字段含义:

CA 'mlx4\_0': IB设备的CA name。一个CA 可以是一张物理形式的IB网卡，也可以是一张IB网卡的一个端口。

Number of ports: IB设备的端口数量。

Node GUID: 代表设备节点的GUID。同一集群环境下的GUID不能冲突。

State: IB设备端口的逻辑状态。

Physical state: IB设备物理层的连接状态。

Rate: IB设备的实时速率。

SM lid: 同一局域网环境下opensm所分配的子网id。

Port GUID: IB端口的GUID。

Link layer: IB设备当前的协议模式。分为 `InfiniBand` 和 `Ethernet` 两个协议类型。

* 查看所有的IB设备

```bash
#ibstat -l
mlx4_0
mlx4_1
```

* 查看所有IB设备端口的GUID

```bash
#ibstat -p
0xec0d9a03000c8981
0xec0d9a03000c8982
0xf45214030033b851
0xf45214030033b852
```

* 展示IB设备中的CA信息

```bash
#ibstat  mlx4_1
CA 'mlx4_1'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0xf45214030033b850
	System image GUID: 0xf45214030033b853
	Port 1:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 1
		LMC: 0
		SM lid: 7
		Capability mask: 0x02514868
		Port GUID: 0xf45214030033b851
		Link layer: InfiniBand
	Port 2:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 4
		LMC: 0
		SM lid: 7
		Capability mask: 0x02514868
		Port GUID: 0xf45214030033b852
		Link layer: InfiniBand
```

* 展示某个IB设备端口的信息

```bash
#ibstat mlx4_0 2
CA: 'mlx4_0'
Port 2:
State: Active
Physical state: LinkUp
Rate: 56
Base lid: 12
LMC: 0
SM lid: 9
Capability mask: 0x02514868
Port GUID: 0xec0d9a03000c8982
Link layer: InfiniBand
```

* 查询IB设备的简要信息(不包含具体端口的信息)

```bash
#ibstat -s
CA 'mlx4_0'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0xec0d9a03000c8980
	System image GUID: 0xec0d9a03000c8983
CA 'mlx4_1'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0xf45214030033b850
	System image GUID: 0xf45214030033b853
```

