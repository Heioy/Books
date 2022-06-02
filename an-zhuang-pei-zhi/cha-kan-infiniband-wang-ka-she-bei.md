# 查看InfiniBand网卡设备

对于任何配置到计算机上的硬件设备，都需要将其对应的信息注册到总线上，而总线会分配一个PCI-Bus编号对应硬件设备。

在操作系统中，最重要的一条总线称为`Peripheral Component Interconnect Express` 总线，简称`PCIe` 总线。而对于计算机上所有的PCIe设备，都可以通过 `lspci` 来查找对应的硬件设备。

需要查找服务器上的`InfiniBand` 网卡，可使用 `lspci | grep -i InfiniBand` 命令来查找。

```bash
#lspci | grep -i InfiniBand
82:00.0 Infiniband controller: Mellanox Technologies MT27500 Family [ConnectX-3]
```

如上，是一张第三代的InfiniBand网卡，通过PCIe总线分配的PCI号为 `82:00.0` 。

一些情况下，可能你需要知道关于这张网卡的详细参数。如，生产商、型号、支持的PCIe通道等信息，可以通过 `lspci -vvv -x -s {pci}` 进行查询

```bash
#lspci -vvv -x -s 82:00.0
82:00.0 Infiniband controller: Mellanox Technologies MT27500 Family [ConnectX-3]
	Subsystem: Mellanox Technologies Device 0050
	Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr- Stepping- SERR- FastB2B- DisINTx+
	Status: Cap+ 66MHz- UDF- FastB2B- ParErr- DEVSEL=fast >TAbort- <TAbort- <MAbort- >SERR- <PERR- INTx-
	Latency: 0, Cache Line Size: 32 bytes
	Interrupt: pin A routed to IRQ 85
	Region 0: Memory at c8800000 (64-bit, non-prefetchable) [size=1M]
	Region 2: Memory at c8000000 (64-bit, prefetchable) [size=8M]
	Expansion ROM at <ignored> [disabled]
	Capabilities: [40] Power Management version 3
		Flags: PMEClk- DSI- D1- D2- AuxCurrent=0mA PME(D0-,D1-,D2-,D3hot-,D3cold-)
		Status: D0 NoSoftRst+ PME-Enable- DSel=0 DScale=0 PME-
	Capabilities: [48] Vital Product Data
		Product Name: CX354A - ConnectX-3 QSFP
		Read-only fields:
			[PN] Part number: MCX354A-FCBT
			[EC] Engineering changes: AC
			[SN] Serial number: MT1604K06557
			[V0] Vendor specific: PCIe Gen3 x8
			[RV] Reserved: checksum good, 0 byte(s) reserved
		Read/write fields:
			[V1] Vendor specific: N/A
			[YA] Asset tag: N/A
			[RW] Read-write area: 105 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 253 byte(s) free
			[RW] Read-write area: 252 byte(s) free
		End
	Capabilities: [9c] MSI-X: Enable+ Count=128 Masked-
		Vector table: BAR=0 offset=0007c000
		PBA: BAR=0 offset=0007d000
	Capabilities: [60] Express (v2) Endpoint, MSI 00
		DevCap:	MaxPayload 512 bytes, PhantFunc 0, Latency L0s <64ns, L1 unlimited
			ExtTag- AttnBtn- AttnInd- PwrInd- RBE+ FLReset+ SlotPowerLimit 116.000W
		DevCtl:	Report errors: Correctable- Non-Fatal+ Fatal+ Unsupported+
			RlxdOrd- ExtTag- PhantFunc- AuxPwr- NoSnoop- FLReset-
			MaxPayload 256 bytes, MaxReadReq 4096 bytes
		DevSta:	CorrErr+ UncorrErr- FatalErr- UnsuppReq+ AuxPwr- TransPend-
		LnkCap:	Port #8, Speed 8GT/s, Width x8, ASPM L0s, Exit Latency L0s unlimited, L1 unlimited
			ClockPM- Surprise- LLActRep- BwNot- ASPMOptComp+
		LnkCtl:	ASPM Disabled; RCB 64 bytes Disabled- CommClk+
			ExtSynch- ClockPM- AutWidDis- BWInt- AutBWInt-
		LnkSta:	Speed 8GT/s, Width x8, TrErr- Train- SlotClk+ DLActive- BWMgmt- ABWMgmt-
		DevCap2: Completion Timeout: Range ABCD, TimeoutDis+, LTR-, OBFF Not Supported
		DevCtl2: Completion Timeout: 65ms to 210ms, TimeoutDis-, LTR-, OBFF Disabled
		LnkCtl2: Target Link Speed: 8GT/s, EnterCompliance- SpeedDis-
			 Transmit Margin: Normal Operating Range, EnterModifiedCompliance- ComplianceSOS-
			 Compliance De-emphasis: -6dB
		LnkSta2: Current De-emphasis Level: -6dB, EqualizationComplete+, EqualizationPhase1+
			 EqualizationPhase2+, EqualizationPhase3+, LinkEqualizationRequest-
	Capabilities: [c0] Vendor Specific Information: Len=18 <?>
	Capabilities: [100 v1] Alternative Routing-ID Interpretation (ARI)
		ARICap:	MFVC- ACS-, Next Function: 0
		ARICtl:	MFVC- ACS-, Function Group: 0
	Capabilities: [148 v1] Device Serial Number 7c-fe-90-03-00-a6-6c-a0
	Capabilities: [154 v2] Advanced Error Reporting
		UESta:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt- UnxCmplt- RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-
		UEMsk:	DLP- SDES- TLP- FCP- CmpltTO- CmpltAbrt+ UnxCmplt+ RxOF- MalfTLP- ECRC- UnsupReq- ACSViol-
		UESvrt:	DLP+ SDES- TLP+ FCP+ CmpltTO+ CmpltAbrt- UnxCmplt- RxOF+ MalfTLP+ ECRC+ UnsupReq- ACSViol-
		CESta:	RxErr- BadTLP- BadDLLP- Rollover- Timeout- NonFatalErr+
		CEMsk:	RxErr+ BadTLP+ BadDLLP+ Rollover+ Timeout+ NonFatalErr+
		AERCap:	First Error Pointer: 00, GenCap+ CGenEn+ ChkCap+ ChkEn+
	Capabilities: [18c v1] #19
	Capabilities: [108 v1] Single Root I/O Virtualization (SR-IOV)
		IOVCap:	Migration-, Interrupt Message Number: 000
		IOVCtl:	Enable- Migration- Interrupt- MSE- ARIHierarchy+
		IOVSta:	Migration-
		Initial VFs: 63, Total VFs: 63, Number of VFs: 0, Function Dependency Link: 00
		VF offset: 1, stride: 1, Device ID: 1004
		Supported Page Size: 000007ff, System Page Size: 00000001
		Region 2: Memory at 0000000000000000 (64-bit, prefetchable)
		VF Migration: offset: 00000000, BIR: 0
	Kernel driver in use: mlx4_core
	Kernel modules: mlx4_core
00: b3 15 03 10 06 04 10 00 00 00 07 02 08 00 00 00
10: 04 00 80 c8 00 00 00 00 0c 00 00 c8 00 00 00 00
20: 00 00 00 00 00 00 00 00 00 00 00 00 b3 15 50 00
30: 00 00 f0 ff 40 00 00 00 00 00 00 00 0f 01 00 00
```

如上，是一张InfiniBand网卡的详细信息。以下，我将对其中部分重要的字段进行解释

(line 3) subsystem: 子系统信息。通常是网卡的生产厂商，正如该网卡是 `Mellanox` 生产的

(line 15) Product Name: 网卡的型号名称，`ConnectX-3` 为第三代产品，`QSFP` 为一种支持热插拔的连接器，使用两路光纤，能够提供更高的数据吞吐能力

(line 17) \[PN] Part number: 网卡名称

(line 19) \[SN] Serial number: 网卡的出厂序列号。用来标识网卡唯一性的凭证

(line 20) \[V0] Vendor specific: 第三代PCIe网卡，使用`x8` 的位宽处理数据

(line 52) LnkCap: 网卡设备所能提供的速率和支持的带宽

(line 56) LnkSta: 网卡的实际速率和当前使用的带宽。需要注意的是，`LnkCap` 和 `LnkSta` 是有区别的，`LnkCap` 代表硬件设备本身所提供的能力，而 `LnkSta` 会受到硬件环境的制约，而产生与硬件所能提供的标准速率不一致的情况。如将支持 `x16` 带宽的网卡设备插在 `x8` 带宽的硬件槽位上。

(line 68) Capabilities: \[148 v1] Device Serial Number: 网卡的GUID。类似于以太网卡的MAC地址

(line 86) Kernel driver in use: 网卡正在使用的驱动

(line 87) Kernel modules: 内核中加载的网卡驱动

(line 88-91) 生产商的代码 (`Vendor ID` : 15b3，Device `ID` : 1003)。同样可以通过 `lspci -nn | grep -i InfiniBand` 查看
