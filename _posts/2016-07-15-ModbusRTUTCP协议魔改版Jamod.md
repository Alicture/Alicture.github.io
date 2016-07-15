---
author: Alicture
comments: true
layout: post
publised: true
title: 魔改版Jamod
category: java
tag: 
- Modbus 
- java
description:
---
> 首先，介绍一下jamod。它是一个用java编写的Modbus协议工具包。可以用它来实现使用java操作串口/端口的Modbus协议设备进行指定的数据操作。[这是它的官方网址](http://jamod.sourceforge.net)上面有详细的介绍。
<!--more-->

当RTU包封装进TCP包中并传输时，通常说交互的设备是采用Modbus RTU over TCP方式的，这就比较特别了。在jamod1.2(最新版本)中并没有得到支持。所以需要对jamod1.2源码进行~~魔改~~二次开发，扩展其功能，使得其完美支持Modbus RTU over TCP。

使得jamod具备支持RTU over TCP的功能，需要完成如下几件事：

1. io包下写：ModbusRTUTCPTransport(底层)
2. net包下：RTUTCPMasterConnection（实现Master的连接建立与断开以及各参数设定）
3. ModbusRTUTCPTransaction（实现事务类）
4. facade包下：ModbusRTUTCPMaster。

经过测试，Badass-jamod1.2具备读取多个寄存器，写入寄存器，读取多个线圈等所有对ModbusRTU over TCP协议设备的数据交互功能。

**注意：读取多个寄存器时，一次最多只能读取32个寄存器。这点与ModbusRTU相同**

传送门：[Badass-jammod1.2](https://github.com/Alicture/Badass-jamod1.2 "play with modbus like a boss!")