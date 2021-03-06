title: nginx
categories:
  - linux
tags:
  - nginx
date: 2021-09-02 11:48:21
---
## nginx config

### 推荐在线配置
```sh
https://digitalocean.github.io/nginxconfig.io/?global.app.lang=zhCN
```

### 静态文件
```sh
server {
    listen 80;
    listen [::]:80;
    server_name blog.viewer.pub;
    root /html/github;
    location / {
       index index.html index.htm;
    }
}
```

### 转发端口
```sh
server {
    listen 80;
    server_name dj.viewer.pub;

    root /html/www/;
    location / {
        proxy_pass http://127.0.0.1:8000/;
    }
}
```

### 重定向
```sh
server {
    listen 80;
    server_name blog.viewer.pub;
    rewrite ^(.*)$ https://blog.viewer.pub; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
# }
```

### https证书

#### 安装certbot
```sh
yay -S --noconfirm certbot
```

#### 静态文件
```sh
sudo certbot certonly -d domain --webroot -w /html/filepath/
```
```sh
server {
    listen 443 ssl;
    server_name blog.viewer.pub;

    ssl_certificate /etc/letsencrypt/live/blog.viewer.pub/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/blog.viewer.pub/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/blog.viewer.pub/chain.pem;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
    ssl_prefer_server_ciphers on;
    location / {
        root /html/github;  #站点目录。
        index index.html index.htm;
    }
}
```

#### 转发端口

```sh
sudo certbot certonly -d domain
```

```sh
server {
    listen 443 ssl;
    server_name nocodb.viewer.pub;

    ssl_certificate /etc/letsencrypt/live/nocodb.viewer.pub/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/nocodb.viewer.pub/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/nocodb.viewer.pub/chain.pem;
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; 
    ssl_prefer_server_ciphers on;
    location / {
        proxy_pass http://127.0.0.1:8080/;
    }
}
```

### 转发mongo端口(TCP)
```sh
stream {
    server {
        listen  <your incoming Mongo TCP port>;
        proxy_connect_timeout 1s;
        proxy_timeout 3s;
        proxy_pass    stream_mongo_backend;
    }

    upstream stream_mongo_backend {
      server <localhost:your local Mongo TCP port>;
  }
}
```