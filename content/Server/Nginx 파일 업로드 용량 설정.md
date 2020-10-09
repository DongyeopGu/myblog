---
title: "Nginx 파일 업로드 용량 설정"
tags: ["Nginx"]
weight: 4
pre: "<b>4. </b>"
---

# Docker-compose & Nginx

>Docker-compose와 Nginx 사용하며 파일 업로드시 용량이 클 경우 해결하기 위한 방법

- default.conf 파일

  ```
  ....
  server {
    listen 443 ssl;
    server_name frontend;
    ssl on; 
    ssl_certificate /ssl/fullchain.pem;
    ssl_certificate_key /ssl/privkey.pem;  
    access_log /var/log/nginx/data-access.log combined;
    location / {
      proxy_pass http://frontend;
      proxy_set_header Host $host;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      client_max_body_size 20M;
      ......
  
  ```

- default.conf 에서 `location` 부분에  `client_max_body_size <용량>;` 추가

