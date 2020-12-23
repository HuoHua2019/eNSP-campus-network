#  2.1版本

[TOC]



## 实现的功能摘要

内部网络用户均使用内网私有地址，集团只申请了100个公网地址：210.96.100.1-100/24，请想办法让内网的用户能用公网地址访问Internet。

<br>

## 设备设置

**路由器**

R1：

```
# 创建NAT地址池和访问控制列表
[R1]nat address-group 1 210.96.100.2 210.96.100.100
[R1]acl 2000

# 制定访问控制规则
[R1-acl-basic-2000]rule 5 permit source 10.0.0.0 0.255.255.255
[R1-acl-basic-2000]int g0/0/1
[R1-GigabitEthernet0/0/1]nat outbound 2000 address-group 1 no-pat
[R1-GigabitEthernet0/0/1]return
<R1>save
  The current configuration will be written to the device. 
  Are you sure to continue? (y/n)[n]:y
  It will take several minutes to save configuration file, please wait.......
  Configuration file had been saved successfully
  Note: The configuration file will take effect after being activated
```

<br>

## 测试

验证动态NAT的配置情况。

```
<R1>dis nat address-group 1

 NAT Address-Group Information:
 --------------------------------------
 Index   Start-address      End-address
 --------------------------------------
 1        210.96.100.2   210.96.100.100
 --------------------------------------
  Total : 1
```

也可以查看NAT会话的详细信息

```
# 为了防止ICMP会话超过其生命周期，故对其进行延长
[R1]firewall-nat session icmp aging-time 300

[R1]display nat session all
[R1]dis nat address-group 1

 NAT Address-Group Information:
 --------------------------------------
 Index   Start-address      End-address
 --------------------------------------
 1        210.96.100.2   210.96.100.100
 --------------------------------------
  Total : 1
[R1]display nat session all
  NAT Session Table Information:

     Protocol          : ICMP(1)
     SrcAddr   Vpn     : 10.2.2.20                                      
     DestAddr  Vpn     : 210.96.100.101                                 
     Type Code IcmpId  : 0   8   47568
     NAT-Info
       New SrcAddr     : 210.96.100.6   
       New DestAddr    : ----
       New IcmpId      : ----

     Protocol          : ICMP(1)
     SrcAddr   Vpn     : 10.2.2.20                                      
     DestAddr  Vpn     : 210.96.100.101                                 
     Type Code IcmpId  : 0   8   47567
     NAT-Info
       New SrcAddr     : 210.96.100.5   
       New DestAddr    : ----
       New IcmpId      : ----

     Protocol          : ICMP(1)
     SrcAddr   Vpn     : 10.2.2.20                                      
     DestAddr  Vpn     : 210.96.100.101                                 
     Type Code IcmpId  : 0   8   47566
```

NAT会话显示动态地址状况，实验成功。

<br>

## 结构图

![image-20201223160017306](https://gitee.com/Huohua2020/Img/raw/master/img/20201223160018.png)