---
title: Serv00 自动重启 pm2
date: 2024-08-22 14:30:59
tags:
- pm2
- serv00
---

## 原理
利用 `curl` 检测 Web 服务器的 HTTP Status Code 来判断服务是否关闭。如果关闭，则使用 SSH 连接到服务器，并重启pm2

## 第1步：登录 Web 面板
根据邮箱中给出的信息，登录到Web面板

## 第2步：开启权限
在Web面板中，依次点击 Additional services --> Run your own applications --> Enabled

## 第3步：登录SSH
根据邮箱中给出的信息登录

## 第4步：创建一个自动重启脚本
在SSH中运行以下命令：
```
touch restart-pm2.sh
```

## 第5步：赋予自动重启脚本权限
在SSH中运行以下命令：
```
chmod +x restart-pm2.sh
```

## 第6步：编辑自动重启脚本
利用 vim 或 Web Panel 中的 File manager 编辑
在 `restart-pm2.sh` 中写入以下内容：
```
#!/bin/bash

USERNAME=''
PASSWORD=''
SSH_ADDRESS=''
SERVER_ADDRESS=''

check_health() {
    local CODE=$(curl -o /dev/null -s -w "%{http_code}\n" --connect-timeout 10 --max-time 30 ${SERVER_ADDRESS})
    if [ "$CODE" = "502" ]; then
        return 1
    fi
    return 0
}

restart_server() {
    echo 'Trying to restart server...'
    local REMOTE_COMMAND1="/home/${USERNAME}/.npm-global/bin/pm2 resurrect && exit" \
    && sshpass -p "${PASSWORD}" ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -tt ${USERNAME}@${SSH_ADDRESS} "$(eval echo $REMOTE_COMMAND1)" \
    && local REMOTE_COMMAND2="/home/${USERNAME}/.npm-global/bin/pm2 restart all && exit" \
    && sshpass -p "${PASSWORD}" ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -tt ${USERNAME}@${SSH_ADDRESS} "$(eval echo $REMOTE_COMMAND2)" 
}

check_health
EXIT_CODE=$?
if [ ${EXIT_CODE} -eq 1 ]; then
    echo 'Server is down! trying to restart server...'

    while [ ${EXIT_CODE} -eq 1 ]; do
        restart_server
        sleep 120
        check_health
        EXIT_CODE=$?
    done

    if [ ${EXIT_CODE} -eq 0 ]; then
        echo 'Server is up!'
    fi
fi

```
更改上述脚本中 `USERNAME`，`PASSWORD`，`SSH_ADDRESS`，`SERVER_ADDRESS` 的值

## 第7步：登录 Web 面板
根据邮箱中给出的信息，登录到Web面板

## 第8步：添加定时任务
在Web面板中，依次点击 Cron jobs --> Add cron job
添加一个每5分钟自动运行 `restart-pm2.sh` 的定时任务，参数如下：
|Key|Value|
|-|-|
|Specify time|Specify manually|
|Form type|Advanced|
|Minute|Every *5*|
|Hour|Every *1*|
|Day of month|Every *1*|
|Month|Every *1*|
|Day of week|Every *1*|
|Command|`/home/<USERNAME>/restart-pm2.sh >/dev/null 2>&1`|
