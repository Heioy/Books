# ibportstate

ibportstate 用来查询IB端口的端口状态和支持的速率等信息，同时还可以进行 禁用、启用、或 重置 交换机端口。通过 ibportstate 来调整IB端口的链接速率、带宽状态。

其命令语法为

```bash
ibportstate [options] <dest dr_path|lid|guid> <portnum> [<op>]
```



#### 命令参数

ibportstate 提供如下参数进行使用

* 支持的 OPTIONS (常用)
  * \--Ca，-C \<ca>: 指定HCA卡名称进行查询或操作。
  * \--Port，-P \<port>：指定HCA卡的端口。
  * \--Direct，-D：使用定向路由
  * \--Lid，-L：使用Lid
  * \--Guid，-G：使用Guid
  * \--sm\_port，-s \<lid>：使用sm lid
  * \--verbose，-v：输出更多信息
  * \--debug，-d：提升调试级别
  * \--version，-V：打印版本信息



* 支持的 ops
  * enable & disable & reset：使能 & 禁用 & 重置交换机端口。仅能对交换机端口进行操作 。
  * on & off：开启 & 禁用HCA网卡端口。
  * speed & width：speed对应输出中的Port:LinkSpeedEnabled；width对应输出中的Port:LinkWidthEnabled。
  * query：查询HCA端口的状态、速率等信息



#### 示例

* 禁用HCA网卡端口

{% hint style="info" %}
使用 `disable` 后可以禁用端口，但使用 `enable` 无法恢复。对于CX-3 网卡，只能通过重启恢复网口正常状态；对于CX-4及之后的HCA卡，可以通过 `mlxfwreset` 重新加载HCA驱动使网卡恢复正常。
{% endhint %}

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
		Link layer: InfiniBan
#ibportstate 3 1 disable 	# ==> ibportstate -L 3 -P 1 disable
Initial CA/RT PortInfo:
# Port info: Lid 3 port 1
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................3
SMLid:...........................3
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................1X
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedActive:.................10.0 Gbps
LinkSpeedExtSupported:...........14.0625 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps
LinkSpeedExtActive:..............14.0625 Gbps
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
# MLNX ext Port info: Lid 3 port 1
StateChangeEnable:...............0x00
LinkSpeedSupported:..............0x01
LinkSpeedEnabled:................0x01
LinkSpeedActive:.................0x00
Disable may be irreversible

After PortInfo set:
# Port info: Lid 3 port 1
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................3
SMLid:...........................3
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................1X
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedActive:.................Extended speed
LinkSpeedExtSupported:...........14.0625 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps
LinkSpeedExtActive:..............14.0625 Gbps
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
#ibstat
CA 'mlx4_0'
	CA type: MT4099
	Number of ports: 2
	Firmware version: 2.42.5000
	Hardware version: 1
	Node GUID: 0x0002c90300a4e720
	System image GUID: 0x0002c90300a4e723
	Port 1:
		State: Down	# 这里显示网卡已经down掉了
		Physical state: Disabled	# 物理链接状态变为disable
		Rate: 2.5
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
		Link layer: InfiniBan
```

* 根据base lid 和端口号查询指定端口的信息

```bash
#ibstat  mlx4_0
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
		SM lid: 11
		Capability mask: 0x02514868
		Port GUID: 0xec0d9a03000c8981
		Link layer: InfiniBand
	Port 2:
		State: Active
		Physical state: LinkUp
		Rate: 56
		Base lid: 12
		LMC: 0
		SM lid: 11
		Capability mask: 0x02514868
		Port GUID: 0xec0d9a03000c8982
		Link layer: InfiniBand

#ibportstate 12 2 query		# 12 ==> Base lid, 2 ==> Port
CA/RT PortInfo:
# Port info: Lid 12 port 2
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................12
SMLid:...........................11
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................4X
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedActive:.................10.0 Gbps
LinkSpeedExtSupported:...........14.0625 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps
LinkSpeedExtActive:..............14.0625 Gbps
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
# MLNX ext Port info: Lid 12 port 2
StateChangeEnable:...............0x00
LinkSpeedSupported:..............0x01
LinkSpeedEnabled:................0x01
LinkSpeedActive:.................0x00
```

* 设置HCA网卡使用的width(修改`Port:LinkWidthEnabled`的值)

{% hint style="info" %}
需要注意的是，设置 `width` 或 `speed` 时，需要根据支持的 `位宽` 或 `速率` 进行设置。以如下为例，设置前支持的位宽(width)为 `1X or 4X`，说明支持两种模式的位宽。设置 `width 1` 时，将切换为 1X；设置为 `width 2` 时，将切换为 4X；设置为 `width 3` 时，将使用 1X or 4X。
{% endhint %}

```bash
#ibportstate 2 1 width 1
Initial CA/RT PortInfo:
# Port info: Lid 2 port 1
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................2
SMLid:...........................18
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................1X or 4X    # 设置前
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedActive:.................10.0 Gbps
LinkSpeedExtSupported:...........14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtActive:..............No Extended Speed
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
# MLNX ext Port info: Lid 2 port 1
StateChangeEnable:...............0x00
LinkSpeedSupported:..............0x01
LinkSpeedEnabled:................0x01
LinkSpeedActive:.................0x01

After PortInfo set:
# Port info: Lid 2 port 1
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................2
SMLid:...........................18
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................1X        # 设置后, 只支持单通道(1X)
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedActive:.................10.0 Gbps
LinkSpeedExtSupported:...........14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtActive:..............No Extended Speed
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
```

* 设置HCA网卡单通道可以使用的速率(修改`Port:LinkSpeedEnabled`的值)

```bash
#ibportstate 2 1 speed 7
Initial CA/RT PortInfo:
# Port info: Lid 2 port 1
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................2
SMLid:...........................18
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................1X or 4X
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps    # 设置前
LinkSpeedActive:.................10.0 Gbps
LinkSpeedExtSupported:...........14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtActive:..............No Extended Speed
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
# MLNX ext Port info: Lid 2 port 1
StateChangeEnable:...............0x00
LinkSpeedSupported:..............0x01
LinkSpeedEnabled:................0x01
LinkSpeedActive:.................0x01

After PortInfo set:
# Port info: Lid 2 port 1
LinkState:.......................Active
PhysLinkState:...................LinkUp
Lid:.............................2
SMLid:...........................18
LMC:.............................0
LinkWidthSupported:..............1X or 4X
LinkWidthEnabled:................1X or 4X
LinkWidthActive:.................4X
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps    # 设置后
LinkSpeedActive:.................10.0 Gbps
LinkSpeedExtSupported:...........14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtEnabled:.............14.0625 Gbps or 25.78125 Gbps
LinkSpeedExtActive:..............No Extended Speed
Mkey:............................<not displayed>
MkeyLeasePeriod:.................0
ProtectBits:.....................0
```

* 重置HCA网卡对于 `LinkSpeedEnabled` & `LinkWidthEnabled` 的设置

```bash
#ibportstate 2 1 query
CA/RT PortInfo:
# Port info: Lid 2 port 1
Lid:.............................2
...
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 10.0 Gbps

#ibportstate 2 1 reset
#ibportstate 2 1 query
CA/RT PortInfo:
# Port info: Lid 2 port 1
...
Lid:.............................2
...
LinkSpeedSupported:..............2.5 Gbps or 5.0 Gbps or 10.0 Gbps
LinkSpeedEnabled:................2.5 Gbps or 5.0 Gbps or 10.0 Gbps
```
