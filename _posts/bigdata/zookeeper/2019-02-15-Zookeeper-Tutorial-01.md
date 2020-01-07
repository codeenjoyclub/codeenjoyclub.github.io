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

### 5. Zookeeper 访问控制  


## 一 、Zookeeper 介绍  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 是一种分布式协调服务。  


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

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;znode 节点维护了一个 stat 结构体，它包含了版本号、时间等数据。  

> &nbsp;&nbsp;&nbsp;&nbsp;zxid ( zookeeper transaction id ) 也叫事务 ID ，具有唯一性。它可以确定操作的先后顺序，如果 zxid1 比 zxid2 小，那么 zxid1 优先于 zxid2 发生。      

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在 Zookeeper 客户端中输入 get 指令可以获得指定节点的值。  

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

#### 1.2 节点类型  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;znode 节点根据存活时间可以划分为临时节点和持久节点。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a) : 临时节点 ( ephemeral )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;临时节点的存活时间和会话有关，当客户端和服务器之间的会话结束时，该节点会自动删除。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b) : 持久节点 ( persistent )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;持久节点的存活时间和会话无关，只有在客户端进行删除节点操作时，节点才会消失。  

> 创建 znode 节点时既可以创建临时节点也可以创建持久节点，但是需要注意的是临时节点不能有子节点。  


<!-- #### 1.3 Time in ZooKeeper   -->

<!-- &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a) : zxid  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;每当 zookeeper 状态发生变化时都会收到一个 zxid ( zookeeper transaction id ) 形式的时间戳。zxid 是唯一的，因此可以通过 zxid 确定操作先后顺序。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b) : version numbers  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;version numbers 简称版本号，每当 znode 节点发生变化时，相应的版本号会增加。 Zookeeper 中三个版本号分别为 cversion 、dataVersion 、aclVersion 。  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;c) : ticks  

当使用 Zookeeper 

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;d) : real time  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 通过在 stat 结构体中放置时间戳参数来记录当前 znode 节点的创建 / 修改时间。   -->




<!-- #### 1.2 节点特性   -->


<!-- ### 2.  -->


## 三 、Zookeeper 会话 ( session )  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 使用 session 来标识客户端与服务器之间的连接状态。Zookeeper 客户端通过创建一个 handle 来建立与服务器之间的连接，一旦创建了 handle ，handle 便处于 connecting 状态并且客户端会尝试和某个服务器进行连接，如果连接成功，handle 的状态便更新成 connected 状态。如果在连接过程中发生了无法恢复的错误，handle 将变成 closed 状态。  

> 无法恢复的错误 / 故障  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;session 过期或认证失败  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;客户端关闭连接  


## 四 、Zookeeper 监听器  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 提供了许多 API 操作，常用的有 create 、 delete 、 exists 、 getData 、 setData 、 getChildren 。其中 exises 、getData 、getChildren 属于读取操作，在进行读取操作的时候可以选择是否设置一个监听器，每当 znode 节点发生变化时，将会触发该节点上已经注册的事件，请求监听的客户端便会接收到相应的异步通知。


## 五 、Zookeeper 访问控制   

create : 可以创建一个子节点

read :  可以从 znode 节点中获取数据以及子节点集合

write : 可以对该节点进行读写操作

delete : 可以进行删除子节点操作

admin : 可以设置权限

## 六 、 Zookeeper 选举机制  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Zookeeper 集群通常是由一个 Leader 以及多个 Follower 组成，其中 Leader 是通过 Zookeeper 内部的选举机制产生。选举时，集群中的节点默认都处于 Looking 状态，每个节点都会向其他节点发起投票，其中包含了服务器 ID 以及最新的事务 ID 。一旦某个节点获得的投票数超过半数时，则该节点会成为 Leader ，其他节点称为 Follower 。