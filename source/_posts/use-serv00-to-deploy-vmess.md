---
title: 使用 Serv00 部署 Vmess
date: 2024-08-21 13:00:15
tags:
---

## 第1步：登录 Web 面板
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
  </tbody>
</table>

## 第5步：登录SSH
根据邮箱中给出的信息登录

## 第6步：安装pm2
在SSH中运行以下命令：
``` shell
bash <(curl -s https://raw.githubusercontent.com/f123ly/serv00/main/install-pm2.sh)
```
安装完成后，需要重新连接SSH，否则 pm2 可能无法使用

## 第7步：下载 Xray
新建一个名为 `xray` 的文件夹，在文件夹中运行以下命令：
``` shell
bash <(curl -s https://raw.githubusercontent.com/f123ly/xray-core/main/download-freebsd-64.sh)
```

## 第8步：配置 Xray
在 `xray` 文件夹中新建 `config,json` 文件：
你可以直接使用以下配置文件，或者去 https://github.com/XTLS/Xray-examples 获取配置文件
修改配置文件中 `port` 的值为第3步获取到的端口
```
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "IPIfNonMatch",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:cn",
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 9100,
            "protocol": "vmess",
            "settings": {
                "clients": [
                  {
                    "id": "9bf37f16-b6ea-4791-a05c-00a3ffayunl1",
                    "alterId": 0
                  }
                ],
                "disableInsecureEncryption": false
              },
            "streamSettings": {
                "network": "ws",
                "security": "none",
                "wsSettings": {
                  "acceptProxyProtocol": false,
                  "path": "/xx",
                  "headers": {}
                }
              },
              "sniffing":{
                "enabled": false,
                "destOverride": [
                  "http",
                  "tls"
                ]
              }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}
```

## 第9步：启动 Xray
在 xray 文件夹中运行以下命令：
``` shell
./xray
```
如果 Xray 正常运行，则进行下一步

## 第10步：使用 pm2 启动 Xray
在 xray 文件夹中依次运行以下命令：
``` shell
pm2 start ./xray
pm2 save
```
