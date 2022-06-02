# 安装firmware



`InfiniBand` 网卡 `firmware` 的安装或升级通常有两种途径，你可以选择在安装 `InfiniBand 驱动`的命令行参数中添加 `--force-fw-update` 或 `--fw-image-dir <fw-path>` 进行 firmware 的安装或升级。

另一种途径则是在安装驱动后，通过 Mellanox 提供的 firmware 固件包进行安装。

关于 InfiniBand 网卡的 firmware 固件包下载可进入 \[FirmWare下载]\([https://network.nvidia.com/support/firmware/firmware-downloads/?ssn=ricrc9ac56alf8lpf6c3ab1551](https://network.nvidia.com/support/firmware/firmware-downloads/?ssn=ricrc9ac56alf8lpf6c3ab1551))。

#### 选择对应型号的网卡下载固件包

_固件包包含 `InfiniBand` 和 `Ethernet` 两种模式，使用 IB 模式点击 `InfiniBand` 通道进行下载。_

![](<../.gitbook/assets/image (2).png>)

解压固件包

```bash
#unzip /tmp/fw-ConnectX3-rel-2_42_5000-MCX354A-FCB_A2-A5-FlexBoot-3.4.752.bin.zip -d /tmp/
```

#### 加载mst服务

`Mellanox Software tools (mst)` 是一款用来管理 `Mellanox` 型号网卡的服务工具，使用该工具可以对 `Mellanox` 型号的网卡进行 `start`、`stop`、`reset`、`enable`等一系列运维操作。

```bash
#mst start
Starting MST (Mellanox Software Tools) driver set
Loading MST PCI module - Success
Loading MST PCI configuration module - Success
Create devices

#mst status
MST modules:
------------
    MST PCI module loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4099_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0000:41:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 01
/dev/mst/mt4099_pci_cr0          - PCI direct access.
                                   domain:bus:dev.fn=0000:41:00.0 bar=0xd4000000 size=0x100000
                                   Chip revision is: 01

```

通过启动 mst 服务后，对应的 MST devices 下面增加了两个设备，这两个设备分别对应此网卡的两个端口。

#### 烧制固件

安装或升级 `firmware` 固件，需要用到 `flint` 工具。

`flint` 具备对`Mellanox InfiniBand` 型号的HCA卡、以太网卡、交换机设备进行烧写firmware 及`Flash 闪存`操作的功能。

```bash
#flint --device /dev/mst/mt4099_pciconf0 -image /tmp/fw-ConnectX3-rel-2_42_5000-MCX354A-FCB_A2-A5-FlexBoot-3.4.752.bin burn

    Current FW version on flash:  2.42.5000
    New FW version:               2.42.5000

    Note: The new FW version is the same as the current FW version on flash.

 Do you want to continue ? (y/n) [n] : y

Burning FS2 FW image without signatures - OK
Restoring signature                     - OK
```

其中，`/dev/mst/mt4099_pciconf0` 设备名称为 `mst status` 中列出的网卡名称；

#### 重启网卡

```bash
#mlxfwreset -d /dev/mst/mt4099_pci_cr0 reset
-E- Unsupported Device: /dev/mst/mt4099_pci_cr0 (ConnectX3).
```

`-E- Unsupported Device` 提示 `Connectx3` 型号的网卡不支持使用 `mlxfwreset` 进行重启，因此需要重启操作系统生效。

#### 查看固件信息

Mellanox 出厂的网卡设备使用 mlxfwmanager 进行网卡固件信息的管理，通过该工具可以进行固件的升级、查询、及管理等工作。

查询 firmware 固件信息可使用 `mlxfwmanager -d <mst-device>` 或 `mlxfwmanager -query` 。

```bash
#mlxfwmanager -d /dev/mst/mt4099_pciconf0
Querying Mellanox devices firmware ...

Device #1:
----------

  Device Type:      ConnectX3
  Part Number:      MCX354A-FCB_A2-A5
  Description:      ConnectX-3 VPI adapter card; dual-port QSFP; FDR IB (56Gb/s) and 40GigE; PCIe3.0 x8 8GT/s; RoHS R6
  PSID:             MT_1090120019
  PCI Device Name:  /dev/mst/mt4099_pciconf0
  Port1 GUID:       0002c90300198dd1
  Port2 GUID:       0002c90300198dd2
  Versions:         Current        Available
     FW             2.42.5000      2.42.5000
     PXE            3.4.0752       3.4.0752

  Status:           Up to date
```

如上为 InfiniBand 网卡的基本信息。
