# ibv\_devices

`ibv_devices` 用来查看服务器上的 IB 设备及 node GUID信息。其手册页中描述如下

```bash
#man ibv_devices
NAME
       ibv_devices - list RDMA devices

SYNOPSIS
       ibv_devices

DESCRIPTION
       List RDMA devices available for use from userspace.
```

使用用法单一且简单。参考如下

```bash
#ibv_devices
    device          	   node GUID
    ------          	----------------
    mlx4_0          	0002c90300a4e720
```

输出中包含两列，`device` 列为HCA卡的设备名称(`CA name`)，`node GUID` 为HCA卡的GUID。

对于 ConnectX-4 系列的HCA网卡，其输出会呈现如下形式。(该系列及之后的CX-5/6的HCA卡，通常每个网口都会使用一个CA name，因此两张双口的HCA卡展示了四个CA设备)

```bash
#ibv_devices
    device          	   node GUID
    ------          	----------------
    mlx5_3          	ec0d9a0300894f0b
    mlx5_2          	ec0d9a0300894f0a
    mlx5_1          	248a07030049d509
    mlx5_0          	248a07030049d508
```

