---
title: 通过 Claw Cloud 部署 3x-ui
date: 2025-06-10T16:33:21+08:00
updated: 2025-06-10T16:33:21+08:00
description: 本教程详细讲解如何通过Claw Cloud容器平台5分钟快速搭建3x-ui代理服务，无需服务器运维经验，小白也能轻松搭建专属代理面板！
tags:
- 3x-ui
- clawcloud
---

## 📝 准备工作
1. 访问 [Claw Cloud 官网](https://claw.cloud/) 
2. 使用一个**注册时间大于180天**的GitHub账户注册Claw Cloud账户（可以领取每月$5的免费额度）

## 🚀 部署步骤

### 第一步：创建应用
1. 点击首页的 **App Launchpad**
2. 选择右上角 **Create App** 按钮

### 第二步：填写镜像配置（Image）
第一栏选择：`Public`  
第二栏输入：`ghcr.io/mhsanaei/3x-ui:latest`

### 第三步：资源配置（Usage）
|资源类型|配置值|
|-|-|
|Replicas|1|
|CPU|0.5 Core|
|Memory|512 MB|

*注意：资源配置可适当提高，但要避免超额*

### 第四步：网络配置（Network）
按此表格添加端口规则：

|Container Port|Public Access|Protocol|
|-|-|--|
|`80`|True|https://|
|`2053`|True|https://|

### 第五步：存储配置（Advanced Configuration → Local Storage）
添加以下持久化存储卷：

|Capacity|Mount Path|
|-|-|
|1 GB|`/etc/x-ui/`|
|1 GB|`/root/cert/`|

*注意：存储配置可适当提高，但要避免超额*

### 最后：部署应用
1. 点击右上角 **Deploy Application** 按钮
2. 等待 2-3 分钟状态变为 **Running**

## 3x-ui 配置
本文仅讲解如何在Claw Cloud上部署3x-ui，3x-ui相关内容详见 [#3x-ui](https://l1yun.github.io/tags/3x-ui/)
