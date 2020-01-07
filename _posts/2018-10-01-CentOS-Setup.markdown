---
layout: post
title:  CentOS Setup
# date:   2018-01-01 08:00:00 +0800
categories: centos setup
tags: [centos]
# no-post-nav: true
---

## 一、新增用户  

### 1. 登录 root 账号  

### 2. 新建用户  

```
   useradd username
```    

### 3. 更改用户密码，激活使用  

```
   passwd 123456
```

### 4. 添加 sudo 权限  

```
   visudo

   username   ALL=(ALL)   ALL
```  


## 二、安装 JDK  

### 1. 上传 JDK1.8 压缩包至指定文件夹  

### 2. 解压 JDK1.8 压缩包并重命名  

```
   tar -xzvf jdk1.8 -C /home/username/modules
```

### 3. 使用 root 用户登录并配置环境变量
 
```
   # JAVA_HOME
   JAVA_HOME=/home/samuel/module/jdk1.8.0

   export PATH=${JAVA_HOME}/bin:$PATH
```

### 4. 刷新环境变量配置，使它生效    

```
   source /etc/profile
```

### 5. 测试 JDK 安装是否成功  

```
   javac -version

   java -version
```