# OpenIBD-网卡配置服务

本小节主要描述 InfiniBand 网卡中关于网卡配置的服务，通过 openibd 服务，你可以对服务器上的任何 Mellanox 生产的网卡进行配置会管理。

#### 驱动加载

当你查看 `openibd` 的服务配置时会看到这么一段描述，`Description=openibd - configure Mellanox devices`。显然，该服务是用来配置 Mellanox 设备的，而其主要功能在于服务器开机/重新加载服务时是否需要加载网卡的驱动模块(_当然，这可以根据你自己进行配置_)。

```bash
#more /usr/lib/systemd/system/openibd.service
[Unit]
Description=openibd - configure Mellanox devices
Documentation=file:/etc/infiniband/openib.conf
After=wickedd.service wickedd-nanny.service local-fs.target sysinit.target
Before=network.target network.service networking.service remote-fs-pre.target
RefuseManualStop=false
DefaultDependencies=false

[Service]
Type=oneshot
TimeoutSec=180
RemainAfterExit=yes
ExecStart=/etc/init.d/openibd start bootid=%b
ExecStop=/etc/init.d/openibd stop
ExecReload=/etc/init.d/openibd restart bootid=%b

[Install]
WantedBy=sysinit.target
```

正如你所看到的，关于服务的众多信息都已经包含在服务配置文件中。而 `openibd` 服务启动需要加载的配置文件 `/etc/infiniband/openib.conf` 出现在其中。而服务启动/关闭时所执行的可执行文件 `/etc/init.d/openibd` 也在其中。

现在你应该已经清楚了 `openibd` 服务启动时会调用 `/etc/init.d/openibd` 可执行文件，然后加载 `/etc/infiniband/openib.conf` 配置文件完成对于IB网卡驱动模块的加载。

好了，了解了 `openibd` 的工作流后，再深入一些看看 `openibd` 服务启动时会加载哪些驱动模块。

```bash
#cat /etc/infiniband/openib.conf
# Start HCA driver upon boot
ONBOOT=yes
...
# Set MAC address of PF via ECPF
# SMARTNIC_PF_MAC_CONF="[<bdf1>-<MAC1>] [<bdf2>-<MAC2>] ..."
# E.g.:
# SMARTNIC_PF_MAC_CONF="0000:03:00.0-c4:8a:07:a5:29:70 0000:03:00.1-c4:8a:07:a5:29:71"
# Load UMAD module
UMAD_LOAD=yes
# Load UVERBS module
UVERBS_LOAD=yes
# Load UCM module
UCM_LOAD=yes
# Load RDMA_CM module
RDMA_CM_LOAD=yes
# Load RDMA_UCM module
RDMA_UCM_LOAD=yes
# Load MLX4 modules
MLX4_LOAD=yes
# Load MLX4_EN module
MLX4_EN_LOAD=yes
# Load MLX5 modules
MLX5_LOAD=yes
# Load IPoIB
IPOIB_LOAD=yes
# Enable IPoIB Connected Mode
SET_IPOIB_CM=auto
...
```

以上为配置文件的部分，其中主要包含了网卡服务启动时是否会随开机而启动(`ONBOOT=yes`)；下半部分带 `_LOAD` 字样的则为IB网卡启动时需要加载的驱动模块。



#### 服务配置

在一些低版本的 `RedHat` 操作系统上，安装了 `InfiniBand` 驱动后 `openibd` 服务默认是没有打开的，需要安装驱动结束后手动启动 `openibd` 服务，确保相应的驱动被加载到内核中。

在 RedHat 7.\* 之后的版本，默认安装驱动后已经开启了 `openibd` 服务，你可以通过 `systemctl status openibd.service` 查看服务状态

```bash
#systemctl status openibd.service
● openibd.service - openibd - configure Mellanox devices
   Loaded: loaded (/usr/lib/systemd/system/openibd.service; enabled; vendor preset: disabled)
   Active: active (exited) since Tue 2022-05-24 10:30:39 CST; 6 days ago
     Docs: file:/etc/infiniband/openib.conf
  Process: 1562 ExecStart=/etc/init.d/openibd start bootid=%b (code=exited, status=0/SUCCESS)
 Main PID: 1562 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/openibd.service
```

倘若需要查看当前 openibd 服务加载了哪些驱动模块，可以通过 /etc/init.d/openibd status 查看

```bash
#/etc/init.d/openibd status

  HCA driver loaded

Configured IPoIB devices:
ib0 ib1

Currently active IPoIB devices:
ib0
ib1
Configured Mellanox EN devices:

Currently active Mellanox devices:
ib0
ib1

The following OFED modules are loaded:

  rdma_ucm
  rdma_cm
  ib_ipoib
  mlx4_core
  mlx4_ib
  mlx4_en
  ib_uverbs
  ib_umad
  ib_ucm
  ib_cm
  ib_core
  mlxfw
```

如果需要加载新的的驱动模块，可将其添加到 `/etc/infiniband/openib.conf` 配置文件中，然后重新启动 `openibd` 服务使新的驱动模块得以加载。

关于启动、停止、重启 `openibd` 服务，无论使用 `systemctl {start|stop|restart} openibd.service` 或 `/etc/init.d/openibd {start|stop|restart}` 都是可以的。

最后，确保 openibd 服务处于开机启动状态，避免出现开机后服务没有启动导致IB网口无法启动的情况。

```bash
#systemctl is-enabled openibd.service
enabled   # 倘若不是 enabled 状态，执行下面的指令将其设置为开机启动服务
#systemctl enable openibd.service
```
