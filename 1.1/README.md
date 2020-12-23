# 1.1版本

[TOC]



## 实现的功能摘要

1. 实现各个PC机之间的通讯
2. 为之后大楼间的通讯打好基础

<br>

## 设备设置

### PC机

**设置IP地址**

PC1：

- IP地址：10.1.1.10
- 子网掩码：255.255.255.0
- 网关：10.1.1.1



PC2：

- IP地址：10.1.2.10
- 子网掩码：255.255.255.0
- 网关：10.1.2.1



PC3：

- IP地址：10.1.1.20
- 子网掩码：255.255.255.0
- 网关：10.1.1.1



PC4：

- IP地址：10.1.2.20
- 子网掩码：255.255.255.0
- 网关：10.1.2.1



### 二层交换机

划分**VLAN**

S4:

```
[S4]vlan b 1 2
Info: This operation may take a few seconds. Please wait for a moment...done.
[S4]int g0/0/1
[S4-GigabitEthernet0/0/1]port link-type trunk
[S4-GigabitEthernet0/0/1]port trunk allow-pass vlan 1 2
[S4-GigabitEthernet0/0/1]int e0/0/1
[S4-Ethernet0/0/1]port link-type access
[S4-Ethernet0/0/1]port default vlan 1
[S4-Ethernet0/0/1]int e0/0/2
[S4-Ethernet0/0/2]port link-type access
[S4-Ethernet0/0/2]port default vlan 2
[S4-Ethernet0/0/2]return
<S4>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]Y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```

S5:

```
[S5]vlan b 1 2
Info: This operation may take a few seconds. Please wait for a moment...done.
[S5]int g0/0/1
[S5-GigabitEthernet0/0/1]port link-type trunk 
[S5-GigabitEthernet0/0/1]port trunk allow-pass vlan 1 2
[S5-GigabitEthernet0/0/1]int e0/0/3
[S5-Ethernet0/0/1]port link-type access 
[S5-Ethernet0/0/1]port default vlan 1
[S5-Ethernet0/0/1]int e0/0/4
[S5-Ethernet0/0/2]port link-type access 
[S5-Ethernet0/0/2]port default vlan 2
[S5-Ethernet0/0/2]return
<S5>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Info: Please input the file name ( *.cfg, *.zip ) [vrpcfg.zip]:
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```



### 三层交换机

```
[S2]vlan b 1 2
Info: This operation may take a few seconds. Please wait for a moment...done.
[S2]int vlan 1
[S2-Vlanif1]ip add 10.1.1.1 24
[S2-Vlanif1]int vlan 2
[S2-Vlanif2]ip add 10.1.2.1 24
[S2-Vlanif2]int g0/0/1
[S2-GigabitEthernet0/0/1]port link-type trunk 
[S2-GigabitEthernet0/0/1]port trunk allow-pass vlan 1 2
[S2-GigabitEthernet0/0/1]int g0/0/2
[S2-GigabitEthernet0/0/2]port link-type trunk
[S2-GigabitEthernet0/0/2]port trunk allow-pass vlan 1 2
[S2-GigabitEthernet0/0/2]return
<S2>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```

<br>

## 测试

以PC1(10.1.1.10)为例，测试各PC机之间的连通性。

```
PC>ping 10.1.2.10

Ping 10.1.2.10: 32 data bytes, Press Ctrl_C to break
From 10.1.2.10: bytes=32 seq=1 ttl=127 time=125 ms
From 10.1.2.10: bytes=32 seq=2 ttl=127 time=94 ms
From 10.1.2.10: bytes=32 seq=3 ttl=127 time=78 ms
From 10.1.2.10: bytes=32 seq=4 ttl=127 time=94 ms
From 10.1.2.10: bytes=32 seq=5 ttl=127 time=78 ms

--- 10.1.2.10 ping statistics ---
  5 packet(s) transmitted
  5 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 78/93/125 ms

PC>ping 10.1.1.20

Ping 10.1.1.20: 32 data bytes, Press Ctrl_C to break
From 10.1.1.20: bytes=32 seq=1 ttl=128 time=94 ms
From 10.1.1.20: bytes=32 seq=2 ttl=128 time=94 ms

--- 10.1.1.20 ping statistics ---
  2 packet(s) transmitted
  2 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 94/94/94 ms

PC>ping 10.1.2.20

Ping 10.1.2.20: 32 data bytes, Press Ctrl_C to break
From 10.1.2.20: bytes=32 seq=1 ttl=127 time=141 ms
From 10.1.2.20: bytes=32 seq=2 ttl=127 time=79 ms
From 10.1.2.20: bytes=32 seq=3 ttl=127 time=78 ms

--- 10.1.2.20 ping statistics ---
  3 packet(s) transmitted
  3 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 78/99/141 ms
```

可以看到，不同VLAN间可以实现通讯。本环节实验成功。



## 结构图

![image-20201223154200448](https://gitee.com/Huohua2020/Img/raw/master/img/20201223154201.png)