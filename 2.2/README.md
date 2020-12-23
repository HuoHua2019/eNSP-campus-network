#  2.2版本

[TOC]



## 实现的功能摘要

禁止大楼1的部门A访问Internet，禁止大楼1的部门B在工作时间（9：00-17：00）访问Internet，允许其他大楼其他部门访问Internet。

<br>

## 设备设置

**路由器**

R1：

```
# 创建时间段
[R1]time-range workday 9:00 to 17:00 working-day

# 配置高级ACL
[R1]acl 3000
[R1-acl-adv-3000]rule deny ip source 10.1.1.0 0.0.0.255 destination 210.96.100.101 0.0.0.0
[R1-acl-adv-3000]rule deny ip source 10.1.2.0 0.0.0.255 destination 210.96.100.101 0.0.0.0 time-range workday
[R1-acl-adv-3000]quit
[R1]int g0/0/0
[R1-GigabitEthernet0/0/0]traffic-filter inbound acl 3000
[R1-GigabitEthernet0/0/0]return
<R1>save
  The current configuration will be written to the device. 
  Are you sure to continue? (y/n)[n]:y
  It will take several minutes to save configuration file, please wait........
  Configuration file had been saved successfully
  Note: The configuration file will take effect after being activated

```

<br>

## 测试

部门A：

以PC1为例，测试PC1能否连接Internet。

```
PC>ping 210.96.100.101

Ping 210.96.100.101: 32 data bytes, Press Ctrl_C to break
Request timeout!
Request timeout!
Request timeout!
Request timeout!
Request timeout!

--- 210.96.100.101 ping statistics ---
  5 packet(s) transmitted
  0 packet(s) received
  100.00% packet loss
```

可以看到，部门A无法访问Internet，实验成功。

部门B的测试留给读者去验证。

<br>

## 结构图

![image-20201223160650325](https://gitee.com/Huohua2020/Img/raw/master/img/20201223160651.png)