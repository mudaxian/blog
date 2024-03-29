---
title: Canoe使用手册
date: 2022-07-12 14:55:40
tags:
- Canoe使用
categories:
- 德宏学习
---

# 一、Canoe基础操作

## 一、新建工程及通道配置

### 1、新建工程

![image-20220813215151712](\img\image-20220813215151712.png)

### 2、添加通道

![image-20220813215543584](\img\image-20220813215543584.png)

### 3、修改通信速率

通信速率应与实际连接的ECU保持一致，一般为500k

![image-20220813215810776](\img\image-20220813215810776.png)

### 4、通道分配

![image-20220813220332872](\img\image-20220813220332872.png)

将与ECU连接的硬件通道 分配给对应的can网络

![image-20220813220535262](\img\image-20220813220535262.png)

![image-20220813220734540](\img\image-20220813220734540.png)

### 5、导入数据库

普通导入

![image-20220813221036338](\img\image-20220813221036338.png)

同时加载数据库和仿真节点

![image-20220813221246405](\img\image-20220813221246405.png)

![image-20220813221423405](\img\image-20220813221423405.png)

### 6、Canoe工程保存

![image-20220813221820781](\img\image-20220813221820781.png)

## 二、Trace窗口

### 1、创建Trace窗口

![image-20220813222539271](\img\image-20220813222539271.png)

### 2、Trace窗口各设置项作用

1、修改为以时间顺序排列报文

![image-20220813222831662](\img\image-20220813222831662.png)

2、删除标题栏某一属性和添加属性

删除：鼠标拖动标题栏需要删除的属性到空白处

添加：选择要添加的属性，用鼠标拖拽到标题栏

![image-20220813223211414](\img\image-20220813223211414.png)

### 3、细节查看

可查看某一条报文的详细信息。

![image-20220813223355386](\img\image-20220813223355386.png)

### 4、统计分析

可统计分析多条报文

![image-20220813223757969](\img\image-20220813223757969.png)

### 5、停止录制can报文后，查看之前的报文

滑动滑块可查看任意时间的报文

![image-20220813224312948](\img\image-20220813224312948.png)

快速查看对应时间戳的报文，右键点击黄色方框

![image-20220813224555445](\img\image-20220813224555445.png)

### 6、设置canoe缓存

![image-20220813224707938](\img\image-20220813224707938.png)

![image-20220813224910071](\img\image-20220813224910071.png)

7、canoe过滤功能

### 8、过滤

1、域过滤器

可过滤不同总线的报文（如只显示can总线报文或只显示lin总线报文

![image-20220813225254159](\img\image-20220813225254159.png)

2、分析过滤器

勾选设置项，从Trace窗口拖拽对应的报文到设置项下

![image-20220813225647933](\img\image-20220813225647933.png)

3、属性列表过滤

![image-20220813230004122](\img\image-20220813230004122.png)

### 9、导出导入报文

![image-20220813230313710](\img\image-20220813230313710.png)

![image-20220813230354668](\img\image-20220813230354668.png)

## 三、Graphic窗口 

### 1、选取信号

可从Trace窗口或GraphicDBC窗口拖拽

![image-20220813230917438](\img\image-20220813230917438.png)

属性栏右键添加信号

![image-20220813231225372](\img\image-20220813231225372.png)

![image-20220813231311996](\img\image-20220813231311996.png)

### 2、设置信号独立显示

![image-20220813231547493](\img\image-20220813231547493.png)

### 3、设置图表显示比例

![image-20220813231702604](\img\image-20220813231702604.png)

### 4、手动调节图表比例

![image-20220813232008452](\img\image-20220813232008452.png)

![image-20220813232127871](\img\image-20220813232127871.png)

### 5、查看报文信息

![image-20220813232423854](\img\image-20220813232423854.png)

### 6、记录文件

在工程开始前配置记录文件，配置完成后开始运行工程

![image-20220813232653927](\img\image-20220813232653927.png)

![image-20220813232835273](\img\image-20220813232835273.png)

开始记录文件

![image-20220813233011609](\img\image-20220813233011609.png)

结束记录文件

![image-20220813233112710](\img\image-20220813233112710.png)

导出/导入记录文件

![image-20220813233209016](\img\image-20220813233209016.png)

### 7、信号高亮显示

![image-20220813233424772](\img\image-20220813233424772.png)

### 8、X-Y轴显示

（针对一些有依赖关系的信号）

![image-20220813233628941](\img\image-20220813233628941.png)