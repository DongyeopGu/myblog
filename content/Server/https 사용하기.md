---
title: "https 사용하기"
tags: ["ssl", "certbot", "https"]
weight: 3
pre: "<b>3. </b>"
---

# https 사용하기(docker, nginx 사용)

> 처음에 openssl 로 .key 와 .cert를 만들어 사용했지만 인증되지 않았기 때문에 <del>https</del>가 나왔음. 그래서 certbot을 이용하여 다시 인증키를 발급받아 사용.

### 1. certbot 이용하여 키 발급

-  `certbot certonly --standalone -d 도메인이름` 으로 키 발급
- 발급이 완료되면 `/etc/letsencrypt/live/도메인이름` 경로에 cert.pem  chain.pem  fullchain.pem  privkey.pem 파일들이 생김

### 2. Nginx 설정

- 나는 default.conf에서 설정하여 실행시켰음
- 생성된 파일을 다른 폴더에 넣어서 사용했음

```default.conf
upstream client {
  server 도메인이름;
}

server {
  listen 80;
  server_name 도메인이름;
  return 301 https://$host$request_uri;
}

server {
  listen 443 ssl;
  server_name 도메인이름; 
  ssl_certificate /ssl/fullchain1.pem;
  ssl_certificate_key /ssl/privkey1.pem;
  
  location / {
    proxy_pass http://도메인이름;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
      }
}
```

### 3. Docker-compose.yml 수정

- default.conf 파일을 docker-compose.yml과 같은 경로에 넣어서 실행시켰음.

```docker-compose.yml
  nginx:
    restart: always
    image: nginx:latest 
    ports:
      - '80:80'    
      - '443:443'
    volumes:
      - './default.conf:/etc/nginx/conf.d/default.conf'
      - '/새로 만든 폴더/ssl/:/ssl/'
```

---

> 구글링을 통해 필요하다고 생각하는 부분만 넣어서 사용했기 때문에 정확하지 않을 수 있음. 이론 공부도 함께 해야할 듯.





