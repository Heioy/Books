# ibhosts

当你需要查看集群内部 `InfiniBand` 主机的拓扑信息，那么 `ibhosts` 可以帮助到你快速的查看集群内部的主机拓扑信息。

ibhosts 既可以运行在具有 IB 网络的集群环境中，同样可以使用在已经保存的拓扑文件中，`ibhosts` 会通过网络环境或拓扑文件提取出其中的HCA主机节点。

`ibhosts` 默认输出节点上的第一张CA卡的拓扑映射，如需查看其他CA的映射信息，可以指定端口或CA名称进行查询。查询时会返回交换机上的所有操作系统主机节点映射信息。

#### 命令参数

`ibhosts` 命令提供了以下几个参数，用来在不同类型下查询集群内部的主机拓扑信息。

* \-C ca\_name: 使用时指定HCA网卡名称来查询对应网口的拓扑映射信息。
* \-P ca\_port: 使用时指定HCA网卡的端口来查询对应端口的拓扑映射信息。
* \-t timeout\_ms: 指定采集HCA节点信息的超时时间。

#### 示例

* 查询集群内部的主机拓扑信息

```bash
#ibhosts
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x7cfe90030017b8e0 ports 2 "node52 HCA-1"
Ca	: 0xec0d9a03000c8980 ports 2 "node22 HCA-1"
```

输出信息中以`line 2` 为例各字段的含义

Ca: 代表一个HCA卡设备，可能是一张网卡的一个端口，也可能是一张网卡。

0x7cfe900300a66ca0: IB网卡的 Node GUID，一个 IB 设备只有一个 Node GUID，但存在多个端口时会有多个 Port GUID。

ports 2: 表示当前IB设备的第2个端口

"sto41 HCA-1": 节点主机名和HCA卡序号。通常节点上存在多个IB设备时，HCA卡序号依次递增。

* 查询指定CA的拓扑映射信息

```bash
#ibdev2netdev
mlx4_0 port 1 ==> ib0 (Up)    # mlx4_0 为CA name，也可用 ibstat 进行查询
mlx4_0 port 2 ==> ib1 (Up)
mlx4_1 port 1 ==> ib2 (Up)
mlx4_1 port 2 ==> ib3 (Up)

#ibhosts -C mlx4_1
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x7cfe900300a66e30 ports 2 "node52 HCA-2"
Ca	: 0xf45214030033b850 ports 2 "node22 HCA-2"
```

* 查询指定port的拓扑映射信息

```bash
#ibhosts -P 1 -C mlx4_1
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x7cfe900300a66e30 ports 2 "node52 HCA-2"
Ca	: 0xf45214030033b850 ports 2 "node22 HCA-2"
```

* 查询拓扑映射文件中的节点拓扑映射关系

```bash
#cat /tmp/topology.out
Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0x7cfe90030017b8e0 ports 2 "node52 HCA-1"
Ca	: 0xec0d9a03000c8980 ports 2 "node22 HCA-1"

#ibhosts /tmp/topology.out
Ca	: 0xcfe900300a66ca0 ports : Ca	: 0x7cfe900300a66ca0 ports 2 "node41 HCA-1"
Ca	: 0x002c90300310450 ports : Ca	: 0x0002c90300310450 ports 2 "node53 HCA-1"
Ca	: 0x002c903009f8450 ports : Ca	: 0x0002c903009f8450 ports 2 "node42 HCA-1"
Ca	: 0xcfe90030017b8e0 ports : Ca	: 0x7cfe90030017b8e0 ports 2 "node52 HCA-1"
Ca	: 0xc0d9a03000c8980 ports : Ca	: 0xec0d9a03000c8980 ports 2 "node22 HCA-1"
```

