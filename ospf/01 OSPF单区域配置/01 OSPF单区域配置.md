# OSPF单区域配置

### 实验目的

- 掌握OSPF单区域的配置方法；
- 掌握OSPF单区域的应用场景；
- 掌握查看OSPF邻居状态的方法；

### 实验内容

本实验模拟企业网络场景。该公司有三大办公区，每个办公区放置了一台路由器，R1放在办公区A，A区的经理的PC-1直接连接R1；R2放在办公区B，B区经理的PC-2直接连接到R2；R3放在办公区C，C区经理的PC-3直接到R3；3台路由器都互相直连，为了能使整个公司网络互相通信，需要再所有路由器上部署路由协议。考虑到公司未来的发展（部门的增加和分公司的成立），为了适应不断发展的网络的需求，公司在所有路由器上部署OSPF协议，且现在所有路由器都属于骨干区域。

<span style="background:#b1ecd3; color:#143b2a;border:dashed 1px #6cd9aa;border-radius:5px;padding:0">router-id为路由器的ID号。 缺省情况下，路由器系统会从当前接口的IP地址中自动选取一个最大值作为Router ID。手动配置Router ID时，必须保证自治系统中任意两台Router ID都不相同。通常的做法是将Router ID配置为与该设备某个接口的IP地址一致。</span>

### 相关命令

```yacas
area area-id
```

- *area-id*：指定区域的标识。其中区域号*area-id*是<span style="background:#dfdfa7; color:#55551c;border:dashed 1px #bfbf60;border-radius:5px;padding:0">0的称为骨干区域</span>。 可以是十进制整数或点分十进制格式。采取整数形式时，取值范围是0～4294967295。 

**ospf**命令用来创建并运行OSPF进程。

```yacas
ospf [ process-id | router-id router-id | vpn-instance vpn-instance-name ] 
ospf process-id
ospf router-id router-id
ospf vpn-instance vpn-instance-name

[Huawei] ospf 100 router-id 10.10.10.1 vpn-instance huawei
```

- *process-id*：OSPF进程号。 整数形式，取值范围是1～65535。缺省值是1。 
- router-id *router-id*：Router ID。 点分十进制格式。 
- vpn-instance vpn-instance-name：指定VPN实例名称。 字符串形式，区分大小写，不支持空格，长度范围是1～31。当输入的字符串两端使用双引号时，可在字符串中输入空格。 

**display ospf brief**命令用来查看OSPF的概要信息。

```yacas
display ospf [ process-id ] brief
```

**display ospf peer**命令用来显示OSPF中各区域邻居的信息。

```yacas
display ospf [ process-id ] peer [ [ interface-type interface-number ] [ neighbor-id ] | brief | last-nbr-down ]
```

### 实验拓扑图

<img src="graph/image-20201105230634158.png" alt="image-20201105230634158" style="zoom:80%;" />

### 实验编址

![img](https://img2018.cnblogs.com/i-beta/1838411/201912/1838411-20191211111427858-844046109.png)

**R1：**

```yacas
<Huawei>un ter mon
Info: Current terminal monitor is off.
<Huawei>sys
Enter system view, return user view with Ctrl+Z.
[Huawei]sysn R1
[R1]int g0/0/2
[R1-GigabitEthernet0/0/2]ip add 172.16.1.254 24
[R1-GigabitEthernet0/0/2]int g0/0/0
[R1-GigabitEthernet0/0/0]ip add 172.16.10.1 24
[R1-GigabitEthernet0/0/0]int g0/0/1
[R1-GigabitEthernet0/0/1]ip add 172.16.20.1 24
[R1-GigabitEthernet0/0/1]q
[R1]
```

R2：

```yacas
<Huawei>un ter mon
Info: Current terminal monitor is off.
<Huawei>sy
Enter system view, return user view with Ctrl+Z.
[Huawei]sysn R2
[R2]int g0/0/2
[R2-GigabitEthernet0/0/2]ip add 172.16.2.254 24
[R2-GigabitEthernet0/0/2]int g0/0/0
[R2-GigabitEthernet0/0/0]ip add 172.16.10.2 24
[R2-GigabitEthernet0/0/0]int g0/0/1
[R2-GigabitEthernet0/0/1]ip add 172.16.20.2 24
[R2-GigabitEthernet0/0/1]q
[R2]
```



```yacas
<Huawei>un ter mon
Info: Current terminal monitor is off.
<Huawei>sy
Enter system view, return user view with Ctrl+Z.
[Huawei]sysn R3
[R3]int g0/0/2
[R3-GigabitEthernet0/0/2]ip add 172.16.3.254 24
[R3-GigabitEthernet0/0/2]int g0/0/0
[R3-GigabitEthernet0/0/0]ip add 172.16.20.3 24
[R3-GigabitEthernet0/0/0]int g0/0/1
[R3-GigabitEthernet0/0/1]ip add 172.16.30.3 24
[R3-GigabitEthernet0/0/1]q
[R3]
```

PC1:

```yacas
172.16.1.1
255.255.255.0
172.16.1.254
```

PC2:

```yacas
172.16.2.1
255.255.255.0
172.16.2.254
```

PC3:

```yacas
172.16.3.1
255.255.255.0
172.16.3.254
```

下一步我们就要进行OSPF的配置了:

```yacas
[R1]ospf 1
[R1-ospf-1]area 0
[R1-ospf-1-area-0.0.0.0]network 172.16.1.0 0.0.0.255
[R1-ospf-1-area-0.0.0.0]network 172.16.10.0 0.0.0.255
[R1-ospf-1-area-0.0.0.0]network 172.16.20.0 0.0.0.255
[R1-ospf-1-area-0.0.0.0]q
[R1-ospf-1]
```

```yacas
[R2]ospf
[R2-ospf-1]area 0
[R2-ospf-1-area-0.0.0.0]network 172.16.2.0 0.0.0.255
[R2-ospf-1-area-0.0.0.0]network 172.16.10.0 0.0.0.255
[R2-ospf-1-area-0.0.0.0]network 172.16.30.0 0.0.0.255
[R2-ospf-1-area-0.0.0.0]q
[R2-ospf-1]
```

```yacas
[R3]ospf
[R3-ospf-1]area 0
[R3-ospf-1-area-0.0.0.0]network 172.16.3.0 0.0.0.255
[R3-ospf-1-area-0.0.0.0]network 172.16.20.0 0.0.0.255
[R3-ospf-1-area-0.0.0.0]network 172.16.30.0 0.0.0.255
[R3-ospf-1-area-0.0.0.0]q
[R3-ospf-1]
```

我们使用OSPF命令创建ospf，然后使用area命令创建区域并进入区域视图，然后使用network命令将相邻的网段添加进去，然后进入路由表查看，这里我们仅举例R1路由器：

```yacas
[R1-ospf-1]q
[R1]dis ip routing-table
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 11       Routes : 11       

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

      127.0.0.0/8   Direct  0    0           D   127.0.0.1       InLoopBack0
      127.0.0.1/32  Direct  0    0           D   127.0.0.1       InLoopBack0
     172.16.1.0/24  Direct  0    0           D   172.16.1.254    GigabitEthernet
0/0/2
   172.16.1.254/32  Direct  0    0           D   127.0.0.1       GigabitEthernet
0/0/2
     172.16.2.0/24  OSPF    10   2           D   172.16.10.2     GigabitEthernet//学到了去往PC2的路由
0/0/0
     172.16.3.0/24  OSPF    10   2           D   172.16.20.3     GigabitEthernet//学到了去往PC3的路由
0/0/1
    172.16.10.0/24  Direct  0    0           D   172.16.10.1     GigabitEthernet
0/0/0
    172.16.10.1/32  Direct  0    0           D   127.0.0.1       GigabitEthernet
0/0/0
    172.16.20.0/24  Direct  0    0           D   172.16.20.1     GigabitEthernet
0/0/1
    172.16.20.1/32  Direct  0    0           D   127.0.0.1       GigabitEthernet
0/0/1
    172.16.30.0/24  OSPF    10   2           D   172.16.20.3     GigabitEthernet
0/0/1
```

查看ospf单区域的配置结果：

```yacas
[R1]display ospf peer 

	 OSPF Process 1 with Router ID 172.16.1.254
		 Neighbors 

 Area 0.0.0.0 interface 172.16.10.1(GigabitEthernet0/0/0)'s neighbors
 Router ID: 172.16.2.254     Address: 172.16.10.2     
   State: Full  Mode:Nbr is  Master  Priority: 1
   DR: 172.16.10.1  BDR: 172.16.10.2  MTU: 0    
   Dead timer due in 37  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:24:37     
   Authentication Sequence: [ 0 ] 

		 Neighbors 

 Area 0.0.0.0 interface 172.16.20.1(GigabitEthernet0/0/1)'s neighbors
 Router ID: 172.16.3.254     Address: 172.16.20.3     
   State: Full  Mode:Nbr is  Master  Priority: 1
   DR: 172.16.20.1  BDR: 172.16.20.3  MTU: 0    
   Dead timer due in 35  sec 
   Retrans timer interval: 5 
   Neighbor is up for 00:23:08     
   Authentication Sequence: [ 0 ] 
```

看看PC间能不能ping通：

```yacas
PC>ping 172.16.3.1

Ping 172.16.3.1: 32 data bytes, Press Ctrl_C to break
From 172.16.3.1: bytes=32 seq=1 ttl=126 time=47 ms
From 172.16.3.1: bytes=32 seq=2 ttl=126 time=78 ms
From 172.16.3.1: bytes=32 seq=3 ttl=126 time=63 ms
From 172.16.3.1: bytes=32 seq=4 ttl=126 time=79 ms
From 172.16.3.1: bytes=32 seq=5 ttl=126 time=78 ms

--- 172.16.3.1 ping statistics ---
  5 packet(s) transmitted
  5 packet(s) received
  0.00% packet loss
  round-trip min/avg/max = 47/69/79 ms
```

