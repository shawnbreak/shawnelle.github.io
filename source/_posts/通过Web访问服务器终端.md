---
title: 通过Web访问浏览器终端
---

有时会遇到这么一种情况，服务器的22端口被限制了，无法ssh登录到设备。或者临时需要在一台没有安装xshell、secureCRT等这些SSH客户端。那么就可以通过Web访问服务器的终端。本文介绍两种工具：cockpit，shellinbox。

## cockpit

[Running Cockpit — Cockpit Project (cockpit-project.org)](https://cockpit-project.org/running.html)

### 安装

```sh
yum install cockpit -y
```

### 配置防火墙

```sh
firewall-cmd --add-service=cockpit --permanent
firewall-cmd --reload
```



### 启动

```sh
systemctl start cockpit.socket
```



### 使用nginx代理 

vim /etc/cockpit/cockpit.conf

允许跨域，关闭ssl，设置根路径

```sh
[WebService]
Origins = https://vm2 https://vm2:9090 wss://vm wss://vm:9090
ProtocolHeader = X-Forwarded-Proto
AllowUnencrypted = true
UrlRoot = /cockpit0/
```



vim  vim /etc/systemd/system/cockpit.socket

因为关闭了ssl，所以为了安全，改成只监听本地端口

```sh
[Socket]
ListenStream=127.0.0.1:9090
```
修改配置后重启cockpit
```sh
systemctl daemon-reload
systemctl restart cockpit
```



修改nginx配置，可通过 https://ip/cockpi0/ 访问 cockpit

```nginx
location ^~/cockpit0/ {
    proxy_pass http://localhost:9090/cockpit0/;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Host $host;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection upgrade;
    proxy_set_header Accept-Encoding gzip;
    proxy_http_version 1.1;
    proxy_buffering off;
    gzip off;
}
```

## shellinabox

说明：通过web访问服务器命令行。

### 安装shellinabox

```sh
yum install shellinabox
```

### 启动shellinabox

```sh
systemctl start shellinaboxd.service
```

访问 https://ip:4200 登录使用

注意，默认不能直接使用root登录。


### 使用nginx代理

1. shellinabox启动参数，禁用ssl，方便nginx代理，改成只监听localhost

vim /etc/sysconfig/shellinabox

修改OPTS为如下， 加上 --localhost-only -t

```sh
OPTS="--disable-ssl-menu --localhost-only -t -s /:LOGIN"
```

2. nginx配置修改

```nginx
 location ^~/shellinabox/ {
        proxy_pass http://127.0.0.1:4200/;
        proxy_redirect default;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        client_max_body_size 10m;
        client_body_buffer_size 128k;

        proxy_connect_timeout 90;
        proxy_send_timeout 90;
        proxy_read_timeout 90;

        proxy_buffer_size 4k;
        proxy_buffers 4 32k;
        proxy_busy_buffers_size 64k;
        proxy_temp_file_write_size 64k;
    }
```

