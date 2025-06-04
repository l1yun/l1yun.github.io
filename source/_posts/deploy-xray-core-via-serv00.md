---
title: 通过Serv00部署Xray-core
date: 2024-08-21T13:00:15+08:00
updated: 2025-04-03T20:57:00+08:00
description: 在Serv00平台上部署Xray-core，并使用pm2进行管理
tags:
- serv00
- xray-core
---

{% note danger %}
由于Serv00在2025年4月3日宣布禁止搭建VPN相关内容，故本教程已失效
详见 [April cleaning and new ToS - Serv00.com](https://forum.serv00.com/d/2787-april-cleaning-and-new-tos)
{% endnote %}

## 第1步：登录Web面板
根据邮箱中给出的信息，登录到Web面板

## 第2步：开启权限
在Web面板中，依次点击 Additional services --> Run your own applications --> Enabled

## 第3步：获取端口
在Web面板中，获取端口

## 第4步：重新绑定域名
在Web面板中，重新绑定域名，你可以使用自己的域名，也可以使用 Serv00 提供的域名
|Key|Value|
|-|-|
|Domain|*example.com*|
|Website Type|proxy|
|Proxy Target|localhost|
|Proxy URL|*留空*|
|Proxy port|*你在第3步时获取的端口*|
|Use HTPPS|False|
|DNS support|True|

## 第5步：登录SSH
根据邮箱中给出的信息登录

## 第6步：安装pm2
在SSH中运行以下命令：
``` shell
bash <(curl -s https://raw.githubusercontent.com/f123ly/serv00/main/install-pm2.sh)
```
安装完成后，需要重新连接SSH，否则 pm2 可能无法使用

## 第7步：下载Xray
新建一个名为 `xray` 的文件夹，在文件夹中运行以下命令：
``` shell
wget https://github.com/XTLS/Xray-core/releases/latest/download/Xray-freebsd-64.zip
unzip Xray-freebsd-64.zip
```

## 第8步：配置Xray
在 `xray` 文件夹中新建 `config,json` 文件：
你可以去 https://github.com/XTLS/Xray-examples 获取配置文件，然后修改配置文件中 `port` 的值为第3步获取到的端口，修改 `uuid` 的值为你的 UUID

## 第9步：启动Xray
在 xray 文件夹中运行以下命令：
``` shell
./xray
```
如果 Xray 正常运行，则按下 Ctrl + C 停止 Xray，并使用 pm2 启动 Xray：
``` shell
pm2 start ./xray
pm2 save
```
