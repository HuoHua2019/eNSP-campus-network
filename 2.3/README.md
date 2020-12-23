#  2.3版本

[TOC]



## 实现的功能摘要

实现路由器和三层交换机的远程telnet管理。

<br>

## 设备设置

**路由器**

R1：

```
[R1]user-interface vty 0 4
[R1-ui-vty0-4]set authentication password cipher 123456
[R1-ui-vty0-4]return
<R1>save
  The current configuration will be written to the device. 
  Are you sure to continue? (y/n)[n]:y
  It will take several minutes to save configuration file, please wait.......
  Configuration file had been saved successfully
  Note: The configuration file will take effect after being activated
```



**交换机**

S1：

```
[S1]user-interface vty 0 4
[S1-ui-vty0-4]set authentication password cipher 123456
[S1-ui-vty0-4]return
<S1>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```

S2：

```
[S2]user-interface vty 0 4
[S2-ui-vty0-4]set authentication password cipher 123456
[S2-ui-vty0-4]return 
<S2>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```

S3：

```
[S3]user-interface vty 0 4
[S3-ui-vty0-4]set authentication password cipher 123456
[S3-ui-vty0-4]return
<S3>save
The current configuration will be written to the device.
Are you sure to continue?[Y/N]y
Now saving the current configuration to the slot 0.
Save the configuration successfully.
```

<br>

## 测试

用S3远程登录S2。

```
<S3>telnet 192.168.1.1
Trying 192.168.1.1 ...
Press CTRL+K to abort
Connected to 192.168.1.1 ...


Login authentication


Password:
Info: The max number of VTY users is 5, and the number
      of current VTY users on line is 1.
      The current login time is 2020-12-23 14:52:03.
<S2>
```

可以看到，S3成功 telnet S2，实验成功。

<br>

## 结构图

![image-20201223162654015](https://gitee.com/Huohua2020/Img/raw/master/img/20201223162702.png)