# OpenSM-子网管理服务

`OpenSM(Open Subnet Manager)` 是适配 InfiniBand 网络的子网管理器，在集群环境中为每个 InfiniBand 网络分配一个 SM 编号并进行管理。在复杂的集群环境中，`OpenSM` 子网最多可分配65536个节点。节点与节点间通常使用 InfiniBand 交换机进行连接，部分交换机中会自带子网管理功能。



#### 服务配置

`OpenSM` 使用 `opensmd` 作为子网管理守护进程，并使用 `Partition Key(PKeys)` 结构的配置进行 `OpenSM` 规则的配置。一般情况下，`OpenSM` 服务启动时会自动查找 `/etc/opensm/partitions.conf` 配置文件。

配置文件中会包含一个默认 Partition，由 OpenSM 所创建。默认 Partition 的 P\_key值为 0x7fff。

默认的 OpenSM 配置文件为

```bash
#cat /etc/opensm/partitions.conf
Default=0x7fff,ipoib:ALL=full;
```



#### 启动 OpenSM 服务

OpenSM 服务以守护进程的方式进行启动，可通过 `opensmd.service` 或 `/etc/init.d/opensmd` 进行启动。

```bash
#systemctl start opensmd.service    # == /etc/init.d/opensmd start
```

