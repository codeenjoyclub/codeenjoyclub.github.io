---
layout: post
title: Hadoop Tutorial 01 ( Hadoop 概述 )
category: hadoop
tags: [hadoop]
---

## 目录  

### 1. Hadoop 是什么  

### 2. Hadoop 发展史  

### 3. Hadoop 的优势与劣势  

### 4. Hadoop 组成  


## 一 、 Hadoop 是什么  

![Hadoop](https://alicloud-samuel.oss-cn-shanghai.aliyuncs.com/gitshenbin.gitee.io/hadoop/Hadoop.png "Hadoop")  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Hadoop 是由 Apache 基金会所开发的分布式基础架构。具有数据存储以及数据高速运算的功能，适合一次写入、多次读出的应用场景。  

<!-- > 分布式系统  
    　&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;分布式系统是指由多个计算机节点组成的系统，节点之间通过网络进行通信。   -->


## 二 、 Hadoop 发展史  


## 三 、 Hadoop 特征  

### 1. 高可靠性  

### 2. 高扩展性  

### 3. 高效性  

### 4. 高容错性  


## 四 、 Hadoop 组成  

### 1. HDFS  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;HDFS ( Hadoop Distributed File System ) 称为 Hadoop 分布式文件系统。用于存储文件，适合一次写入、多次读出的场景，具有高可靠、高吞吐量的特性。  

### 2. MapReduce  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;MapReduce 是一种分布式的计算框架。指定一个 Map 函数和一个 Reduce 函数，分别用于把一组键值对映射成一组新的键值对 ( Map ) 和保证所有映射的键值对中的每一个共享相同的键组 ( Reduce )。  

### 3. YARN  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;传统的 MapReduce 架构拥有 JobTracker 和 TaskTracker 两个组件，存在单点故障以及资源利用率低等问题。 YARN 将 JobTracker 的功能进行拆分，避免了传统架构的单点故障问题以及资源利用率问题。  

### 4. Common  

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Common 是 Hadoop 中的一个辅助工具，为其他模块提供工具支持。