# ibstatus

`ibstatus` 命令行工具用来查询 `InfiniBand` 设备的基本信息，与 `ibstat` 功能基本一致。区别在于展示内容上略有区别，`ibstatus` 输出信息更为精简。



#### 命令参数

`ibstatus` 中提供的参数如下

* devname：查看指定设备的所有端口信息
* devname:portnum：查看指定设备的指定端口



#### 示例

* 查看所有IB设备的信息

```bash
#ibstatus
Infiniband device 'mlx4_0' port 1 status:
	default gid:	 fe80:0000:0000:0000:0002:c903:00a4:e721
	base lid:	 0x3
	sm lid:		 0x3
	state:		 4: ACTIVE
	phys state:	 5: LinkUp
	rate:		 56 Gb/sec (4X FDR)
	link_layer:	 InfiniBand

Infiniband device 'mlx4_0' port 2 status:
	default gid:	 fe80:0000:0000:0000:0002:c903:00a4:e722
	base lid:	 0x2
	sm lid:		 0x2
	state:		 4: ACTIVE
	phys state:	 5: LinkUp
	rate:		 56 Gb/sec (4X FDR)
	link_layer:	 InfiniBand
```

* 查看指定设备的所有端口信息

{% hint style="info" %}
对于 ConnectX-3 系列的IB设备，通常为一卡双口。且两个网口都是用相同的设备名，去别载于端口号不同。对于 ConnectX-4 及之后系列的IB设备，则通常一个网口使用一个单独的CA name。
{% endhint %}

```bash
#ibstatus mlx4_0
Infiniband device 'mlx4_0' port 1 status:
	default gid:	 fe80:0000:0000:0000:0002:c903:00a4:e721
	base lid:	 0x3
	sm lid:		 0x3
	state:		 4: ACTIVE
	phys state:	 5: LinkUp
	rate:		 56 Gb/sec (4X FDR)
	link_layer:	 InfiniBand

Infiniband device 'mlx4_0' port 2 status:
	default gid:	 fe80:0000:0000:0000:0002:c903:00a4:e722
	base lid:	 0x2
	sm lid:		 0x2
	state:		 4: ACTIVE
	phys state:	 5: LinkUp
	rate:		 56 Gb/sec (4X FDR)
	link_layer:	 InfiniBand
```

* 查询端口1的基本信息

```bash
#ibstatus mlx4_0:1
Infiniband device 'mlx4_0' port 1 status:
	default gid:	 fe80:0000:0000:0000:0002:c903:00a4:e721
	base lid:	 0x3
	sm lid:		 0x3
	state:		 4: ACTIVE
	phys state:	 5: LinkUp
	rate:		 56 Gb/sec (4X FDR)
	link_layer:	 InfiniBand
```

