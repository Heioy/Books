# ibnodes

`ibnodes` 用来展示同一交换机上的所有节点拓扑信息，包含物理主机与交换机信息。其用法与 `ibhosts` 基本一致。



#### 命令参数

`ibnodes` 命令提供了以下几个参数，用来在不同类型下查询集群的节点拓扑信息。

* \-C ca\_name: 使用时指定HCA网卡名称来查询对应网口的拓扑映射信息。
* \-P ca\_port: 使用时指定HCA网卡的端口来查询对应端口的拓扑映射信息。
* \-t timeout\_ms: 指定采集HCA节点信息的超时时间。



#### 示例

* 查询集群的节点拓扑信息

```bash
#ibnodes
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x7cfe90030017b8e0 ports 2 "node52 HCA-1"
Ca	: 0xec0d9a03000c8980 ports 2 "node22 HCA-1"
Switch	: 0x0002c903008f8700 ports 36 "SwitchX -  Mellanox Technologies" base port 0 lid 60 lmc 0
```

`ibnodes` 的输出与 `ibhosts` 相比，多出了交换机的信息。

以 `(line 7)` 为例，其含义为该交换机包含36个端口，`"SwitchX - Mellanox Technologies"` 为交换机的主机名。`lid 60` 表示该交换机的SM Lid 为60。

* 查询指定HCA网卡相关的节点拓扑

```bash
#ibnodes -C mlx4_1
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x7cfe900300a66e30 ports 2 "node52 HCA-2"
Ca	: 0xf45214030033b850 ports 2 "node22 HCA-2"
Switch	: 0x0002c903006cf2f0 ports 36 "MF0;switch-A:SX6036/U1" enhanced port 0 lid 6 lmc 0
```

* 查询指定端口的节点拓扑

```bash
#ibnodes -C mlx4_1 -P 1
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x7cfe900300a66e30 ports 2 "node52 HCA-2"
Ca	: 0xf45214030033b850 ports 2 "node22 HCA-2"
Switch	: 0x0002c903006cf2f0 ports 36 "MF0;switch-A:SX6036/U1" enhanced port 0 lid 6 lmc 0
```
