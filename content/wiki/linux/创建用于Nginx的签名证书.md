---
title: "创建用于Nginx的签名证书"
date: 2017-10-15T22:10:09+08:00
draft: false
categories: ["Linux"]
tags: ["nginx", "https", "证书"]
---

Nginx配置SSL时创建签名证书的方法
<!--more-->

### 使用OpenSSL创建证书

生成RSA密钥

``` sh
# 生成RSA密钥
openssl genrsa -des3 -out server.key 1024
```

需要设置密码，请记住

``` sh
# 生成已经证书请求
openssl req -new -key server.key -out server.csr
```

此步骤需要输入证书信息，需要注意的是Common Name最好设置为需要使用此证书的主机名

``` sh
cp server.key server.key.org
openssl rsa -in server.key.org -out server.key
# 使用密钥和CSR对证书进行签名
# 生成v1版证书
openssl x509 -req -days 3650 -sha256 -in server.csr -signkey server.key -out server.crt
# 还会有v3版证书，待写
```

### Nginx配置

server中

``` nginx
listen 8090 ssl;
ssl on;
ssl_certificate nginxcert/nginx.crt;
ssl_certificate_key nginxcert/nginx.key;

# 此配置会设置X-Forwarded-Proto值为https，供后台应用调用
proxy_set_header X-Forwarded-Proto  $scheme;
proxy_set_header Host $host:$server_port;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
# 此配置将后台应用返回的http转成https
#proxy_redirect ~^http://([^:]+):$server_port(/.+)$ https://$1:$server_port$2;
#让http请求重定向到https请求
error_page 497  https://$host:$server_port$request_uri;
```
