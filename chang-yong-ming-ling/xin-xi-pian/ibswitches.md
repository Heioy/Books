# ibswitches

`ibswitches` 用来查看当前组网环境中的 IB交换机设备。使用时可以直接在终端输入 `ibswitches` ，这将会输出当前组网拓扑中的所有交换机设备，或者接收一个拓扑映射文件，`ibswitches` 将从拓扑中解析出相关的交换机设备。

{% hint style="info" %}
很多情况下，通过 `ibswitches`、`iblinkinfo`、`ibportstate` 等输出的信息中只包含了第一张网卡的设备、交换机等信息。若要查询所有的信息，需要指定不同的网卡名称进行查询。
{% endhint %}



#### 命令参数

`ibswitches` 命令参数如下

* \-C \<ca\_name>: 指定CA name，查看对应网口相关的交换机设备信息
* \-P \<ca\_port>: 指定端口号，查询指定端口所连接的交换机设备信息
* \-t \<timeout\_ms>: 设置命令执行超时时间。



#### 示例

* 查看当前组网中的所有 IB 交换机设备

```bash
#ibswitches
Switch	: 0xe41d2d03003bfb80 ports 36 "MF0;node231:SX6036/U1" enhanced port 0 lid 12 lmc 0
Switch	: 0x001777fffe2b5b65 ports 2 "Obsidian Longbow C400 - LBC4xxxxx" base port 0 lid 11 lmc 0
Switch	: 0x001777fffea79b9f ports 2 "Obsidian Longbow C400 - LBC4xxxxx" base port 0 lid 13 lmc 0
Switch	: 0xe41d2d03003251c0 ports 36 "MF0;node245:SX6036/U1" enhanced port 0 lid 16 lmc 0
```

* 查看指定CA连接的交换机设备信息

```bash
#ibdev2netdev
mlx5_0 port 1 ==> ib0 (Up)
mlx5_1 port 1 ==> ib1 (Up)
mlx5_2 port 1 ==> ib2 (Up)
mlx5_3 port 1 ==> ib3 (Up)

#ibswitches -C mlx5_0
Switch	: 0xe41d2d03003bfb80 ports 36 "MF0;node231:SX6036/U1" enhanced port 0 lid 12 lmc 0
Switch	: 0x001777fffe2b5b65 ports 2 "Obsidian Longbow C400 - LBC4xxxxxx" base port 0 lid 11 lmc 0
Switch	: 0x001777fffea79b9f ports 2 "Obsidian Longbow C400 - LBC4xxxxxx" base port 0 lid 13 lmc 0
Switch	: 0xe41d2d03003251c0 ports 36 "MF0;node245:SX6036/U1" enhanced port 0 lid 16 lmc 0
```

* 查看指定端口连接的交换机设备

```bash
#ibswitches -C mlx5_2 -P 1
Switch	: 0x7cfe900300ecf6c2 ports 37 "MF0;node232:SX6036/U1" enhanced port 0 lid 11 lmc 0
Switch	: 0x001777fffe768aa2 ports 2 "Obsidian Longbow C400 - LBC4xxxxxx" base port 0 lid 9 lmc 0
Switch	: 0x001777fffe56b6ca ports 2 "Obsidian Longbow C400 - LBC4xxxxxx" base port 0 lid 12 lmc 0
Switch	: 0xf4521403007deac0 ports 36 "MF0;node241:SX6036/U1" enhanced port 0 lid 42 lmc 0
```
