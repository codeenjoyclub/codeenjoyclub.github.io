---
layout: post
title: Hadoop Tutorial 02 ( Hadoop HDFS 概述 )
category: hadoop
tags: [hadoop]
---

## 目录  

### 1. HDFS 是什么  

### 2. HDFS 架构  

### 3. HDFS 数据流  

### 4. NameNode 工作机制  

### 5. DataNode 工作机制  


## 一 、 HDFS 是什么  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HDFS ( Hadoop Distributed File System ) 称为 Hadoop 分布式文件系统。用于存储文件，通过目录树来定位文件所在位置。具有高可靠、高吞吐量的特性，适合一次写入、多次读出的场景。  


## 二 、 HDFS 架构  

### 1. Block 数据块  


### 2. NameNode  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;NameNode 是一个中心服务器，主要用于存储文件系统的元数据以及每个文件所对应的的块信息。  

### 3. DataNode  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;DataNode 主要用于存储文件数据块信息，每一个数据块都可以在多个 DataNode 中存储多个副本。  

### 4. Secondary NameNode  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Secondary NameNode 用来监控 HDFS 状态的辅助后台程序，每隔一段时间 Secondary NameNode 会自动获取 HDFS 元数据的快照。  


## 三 、 HDFS 数据流  


## 四 、 NameNode 工作机制  


## 五 、 DataNode 工作机制  

