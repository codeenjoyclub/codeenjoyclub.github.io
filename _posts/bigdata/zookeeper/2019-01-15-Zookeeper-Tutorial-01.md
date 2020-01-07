---
layout: post
title: Zookeeper Tutorial 01 ( Zookeeper 概述 )
category: zookeeper
tags: [zookeeper]
---

## 目录  

### 1. Zookeeper 介绍  

### 2. Zookeeper 数据模型  

### 3. Zookeeper 会话  

### 4. Zookeeper 监听器  

### 5. Zookeeper 应用场景  


## 一 、Zookeeper 介绍  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 是一种分布式协调服务。  

> 分布式  :   
    <br>
    协调服务 :  


## 二 、Zookeeper 数据模型  

![Zookeeper 数据模型](https://alicloud-samuel.oss-cn-shanghai.aliyuncs.com/gitshenbin.gitee.io/zookeeper/Zookeeper%E6%95%B0%E6%8D%AE%E6%A8%A1%E5%9E%8B.png "Zookeeper 数据模型")  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 数据模型和数据结构中的树类似，由节点组成。不同之处在于每个节点可以存储数据。    

### 1. 节点 ( ZNodes )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 中节点被称为 znode ，可以用来存储数据。对于不同位置的节点，znode 采用路径引用的方式进行引用，这种方式使每个 znode 节点可以通过它所在的路径进行唯一标识。    

> 节点引用方式：  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;znode1 : &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; /znode1  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;znode2 : &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; /znode2  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;children1 : &nbsp;&nbsp;&nbsp;&nbsp; /znode1/children1  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;children2 : &nbsp;&nbsp;&nbsp;&nbsp; /znode1/children2  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;children3 : &nbsp;&nbsp;&nbsp;&nbsp; /znode2/children3  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;children4 : &nbsp;&nbsp;&nbsp;&nbsp; /znode2/children4  


#### 1.1 stat 结构体 ( stat structure )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;znode 节点维护了一个 stat 结构体，它包含了版本号、时间戳等数据。每次 zookeeper 状态发生变化时都会收到一个 zxid 形式的时间戳。  

> &nbsp;&nbsp;&nbsp;&nbsp;zxid ( zookeeper transaction id ) 也叫事务 ID ，具有唯一性。它可以确定操作的先后顺序，如果 zxid1 比 zxid2 小，那么 zxid1 优先于 zxid2 发生。      

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在 Zookeeper 客户端中输入 get 指令可以获得指定节点的数据。  

```
[zk: localhost:2181(CONNECTED) 0] get /

cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x0
cversion = -1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1

======================================

[zk: localhost:2181(CONNECTED) 1] create /node1 "node_context"
Created /node1
[zk: localhost:2181(CONNECTED) 2] get /node1
"node_context"
cZxid = 0x100000002
ctime = Wed Dec 25 23:03:56 CST 2019
mZxid = 0x100000002
mtime = Wed Dec 25 23:03:56 CST 2019
pZxid = 0x100000002
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 14
numChildren = 0
```

> node_context &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点存储的数据，每个节点存储的数据大小不能超过 1M 。  
cZxid &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点创建的事务 ID 。   
ctime &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点创建的时间 。    
mZxid &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点修改的事务 ID 。    
mtime &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点修改的时间。    
pZxid &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 当前 znode 节点中子节点最后一次修改的事务 ID 。    
cversion &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 子节点版本号。  
dataVersion &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 数据版本号。每次对 znode 节点进行 set 操作时， dataVersion 都会增加 1 。  
aclVersion &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : 访问控制 ( ACL - Access Control List ) 版本号。  
ephemeralOwner &nbsp;&nbsp; : 临时节点的 session id 。如果当前节点不是临时节点，则为 0 。  
dataLength &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点的数据长度。  
numChildren &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; : znode 节点的子节点数量。  

#### 1.2 会话 ( session )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 通过 session 来标识客户端与服务器之间的连接状态。Zookeeper 客户端通过创建一个 handle 来建立与服务器之间的连接，一旦创建了 handle ，handle 便处于 connecting 状态并且客户端会尝试和某个服务器进行连接，如果连接成功，handle 的状态便更新成 connected 状态。如果在连接过程中发生了无法恢复的错误，handle 将变成 closed 状态。  

> 无法恢复的错误 / 故障  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;连接超时 ( session 过期 )  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;认证 / 授权失败  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;客户端关闭连接  


#### 1.3 节点类型  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;znode 节点根据存活时间可以划分为临时节点和持久节点。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a) : 临时节点 ( ephemeral nodes )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;临时节点的存活时间和会话有关，当客户端和服务器之间的会话结束时，该节点会自动删除。  

> 创建 znode 节点时既可以创建临时节点也可以创建持久节点，但是需要注意的是临时节点不能有子节点。  

> 使用 create -e 指令可以创建临时节点  

```
[zk: localhost:4180(CONNECTED) 4] create -e /xing/ei world     
Created /xing/ei  
```

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b) : 持久节点 ( persistent nodes )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;持久节点的存活时间和会话无关，只有在客户端进行删除节点操作时，节点才会消失。  

> 使用 create 指令可以创建持久节点  

```
[zk: localhost:4180(CONNECTED) 4] create /xing/ei world     
Created /xing/ei  
```




&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 支持创建顺序节点 ( sequence nodes )，在创建顺序节点时，Zookeeper 服务器会在指定的节点名称后面添加一个递增的数字序列。  

> 使用 create -s 指令可以创建顺序节点  

```
[zk: localhost:4180(CONNECTED) 0] create -s /xing/item world  
Created /xing/item0000000001  
[zk: localhost:4180(CONNECTED) 1] create -s /xing/item world  
Created /xing/item0000000002  
[zk: localhost:4180(CONNECTED) 2] create -s /xing/item world  
Created /xing/item0000000003  
[zk: localhost:4180(CONNECTED) 3] create -s /xing/item world  
Created /xing/item0000000004  
```

> 因此可以创建同名的 znode 节点 ( 不同之处 )  