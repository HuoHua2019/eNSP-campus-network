#  2.0版本

[TOC]



## 实现的功能摘要

1. 实现大楼间的通信
2. 整个内部网络中使用动态路由协议

<br>

## 设备设置

**路由器**

R1：

```
# 首先将路由器加入OSPF
[R1]ospf 1 router-id 4.4.4.4
[R1-ospf-1]area 0
[R1-ospf-1-area-0.0.0.0]network 192.168.3.0 0.0.0.255

# 配置路由器GE 0/0/0端口地址为192.168.3.1 24
# 配置路由器GE 0/0/1端口地址为210.96.100.1
[R1-ospf-1-area-0.0.0.0]int g0/0/1
[R1-GigabitEthernet0/0/1]ip add 210.96.100.1 24
[R1-GigabitEthernet0/0/1]int g0/0/0
[R1-GigabitEthernet0/0/0]ip add 192.168.3.1 24
[R1-GigabitEthernet0/0/0]quit
[R1]ip route-static 0.0.0.0 0.0.0.0 g0/0/1
[R1]ospf 1
[R1-ospf-1]default-route-advertise
[R1-ospf-1]return
<R1>save
  The current configuration will be written to the device. 
  Are you sure to continue? (y/n)[n]:y
  It will take several minutes to save configuration file, please wait......
  Configuration file had been saved successfully
  Note: The configuration file will take effect after being activated
```



**核心交换机**

S1：

```
# 首先设置vlan1 2 3并添加对应的ip
[S1]vlan b 1 2 3
Info: This operation may take a few seconds. Please wait for a moment...done.
[S1]int v1
[S1-Vlanif1]ip add 192.168.1.2 24
[S1-Vlanif1]int v2
[S1-Vlanif2]ip add 192.168.2.2 24
[S1-Vlanif2]int v3
[S1-Vlanif3]ip add 192.168.3.2 24

# 设置g0/0/1 g0/0/2 g0/0/3对应的vlan
[S1-Vlanif3]int g0/0/1
[S1-GigabitEthernet0/0/1]port link-type access 
[S1-GigabitEthernet0/0/1]port default vlan 1
[S1-GigabitEthernet0/0/1]int g0/0/2
[S1-GigabitEthernet0/0/2]port link-type access
[S1-GigabitEthernet0/0/2]port default vlan 2
[S1-GigabitEthernet0/0/2]int g0/0/3
[S1-GigabitEthernet0/0/3]port link-type access
[S1-GigabitEthernet0/0/3]port default vlan 3
[S1-GigabitEthernet0/0/3]quit

# 设置ospf
[S1]ospf 1 router-id 1.1.1.1
[S1-ospf-1]area 0
[S1-ospf-1-area-0.0.0.0]network 192.168.1.0 0.0.0.255
[S1-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
[S1-ospf-1-area-0.0.0.0]network 192.168.3.0 0.0.0.255
[S1-ospf-1-area-0.0.0.0]return
<S1>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```



**三层交换机**

S2：

```
[S2]int v3
[S2-Vlanif3]ip add 192.168.1.1 24
[S2-Vlanif3]quit

[S2]int g0/0/3	
[S2-GigabitEthernet0/0/3]port link-type access
[S2-GigabitEthernet0/0/3]port default vlan 3
[S2-GigabitEthernet0/0/3]quit

[S2]ospf 1 router-id 2.2.2.2
[S2-ospf-1]area 0
[S2-ospf-1-area-0.0.0.0]network 192.168.1.0 0.0.0.255
[S2-ospf-1-area-0.0.0.0]network 10.1.1.0 0.0.0.255
[S2-ospf-1-area-0.0.0.0]network 10.1.2.0 0.0.0.255
[S2-ospf-1-area-0.0.0.0]return
<S2>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```



S3:

```
[S3]int v3
[S3-Vlanif3]ip add 192.168.2.1 24
[S3-Vlanif3]quit

[S3]int g0/0/3
[S3-GigabitEthernet0/0/3]port link-type access 
[S3-GigabitEthernet0/0/3]port default vlan 3
[S3-GigabitEthernet0/0/3]quit

[S3]ospf 1 router-id 3.3.3.3
[S3-ospf-1]area 0
[S3-ospf-1-area-0.0.0.0]network 192.168.2.0 0.0.0.255
[S3-ospf-1-area-0.0.0.0]network 10.2.1.0 0.0.0.255
[S3-ospf-1-area-0.0.0.0]network 10.2.2.0 0.0.0.255
[S3-ospf-1-area-0.0.0.0]return
<S3>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```

<br>

## 测试

以PC1(10.1.1.10)为例，测试各PC机之间的连通性。

```
PC>ping 10.2.1.10

Ping 10.2.1.10: 32 data bytes, Press Ctrl_C to break
From 10.2.1.10: bytes=32 seq=1 ttl=125 time=141 ms
From 10.2.1.10: bytes=32 seq=2 ttl=125 time=125 ms
From 10.2.1.10: bytes=32 seq=3 ttl=125 time=109 ms
From 10.2.1.10: bytes=32 seq=4 ttl=125 time=141 ms
From 10.2.1.10: bytes=32 seq=5 ttl=125 time=109 ms

--- 10.2.1.10 ping statistics ---
  5 packet(s) transmitted
  5 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 109/125/141 ms

PC>ping 10.2.1.10

Ping 10.2.1.10: 32 data bytes, Press Ctrl_C to break
From 10.2.1.10: bytes=32 seq=1 ttl=125 time=125 ms
From 10.2.1.10: bytes=32 seq=2 ttl=125 time=109 ms
From 10.2.1.10: bytes=32 seq=3 ttl=125 time=110 ms

--- 10.2.1.10 ping statistics ---
  3 packet(s) transmitted
  3 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 109/114/125 ms
```

可以看到，不同大楼的PC间可以实现通讯。本环节实验成功。

<br>

## 结构图

![image-20201223155217713](https://gitee.com/Huohua2020/Img/raw/master/img/20201223155219.png)