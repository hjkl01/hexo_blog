---
title: socks5 转 http 之 privoxy
categories:
  - linux
tags:
  - proxy
date: 2021-07-14 17:45:00
---

## socks5 转 http 

#### privoxy 配置 

```sh
yay -S privoxy

cd /etc/privoxy

(sudo) mv config config.bak
(sudo) vi config

forward-socks5t / 127.0.0.1:1080 .
listen-address 127.0.0.1:9888

sudo systemctl restart privoxy.service
sudo systemctl enable privoxy.service
```
