---
title: jenkins意外宕机的解决记录
date: 2021-12-27 14:57:04
tags: [jenkins, nginx]
categories: [freebie, backend]
---

<!-- more -->

## 更新

------

### [2021-12-27]

- Initial release

## 问题描述

------

部署在阿里云 `centos` 机器上的 `jenkins` 意外挂掉了，记录一下解决办法。

## 解决办法

------

1. 重启 jenkins

```bash
#cd <jenkins 目录>
cd /opt/work_tools/jenkins

# 直接执行 war 包
# 端口号随意
java -jar ./jenkins.war --httpPort=8088
```

2. 修改 nginx 配置

```bash
server {
        listen       443 ssl http2;
        listen       [::]:443 ssl http2;
        server_name  jenkins.config.yyge.top;
        root         /usr/share/nginx/html;

        ssl_certificate "/etc/nginx/cert/20210312/5308292_www.jenkins.config.yyge.top.pem";
        ssl_certificate_key "/etc/nginx/cert/20210312/5308292_www.jenkins.config.yyge.top.key";
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        # 开启错误页面自定义
        fastcgi_intercept_errors on;

        # 自定义 Request Body 上传文件大小
        client_max_body_size    1000m;

        location / {
                # 更改此处的端口号
                proxy_pass http://127.0.0.1:8088;
        }

        error_page 404 /index.html;
        location = /index.html {
                root /study/frontend/practise/githubGoods/public/ddzy.github.io;          }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
}
```