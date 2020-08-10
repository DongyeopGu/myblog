---
title: "Docker-compose 이용하기"
tags: ["Docker"]
weight: 1
pre: "<b>1. </b>"
---

# Docker-compose를 이용한 배포 

> Docker compose를 이용하여 배포했던 것 정리. 
>
> 혼자 찾아보고 진행했던 내용이기 때문에 정확하지 않을 수 있음.

### 1. docker-compose.yml 파일 만들기

- docker 실행 옵션을 미리 작성하고 편하게 사용하기 위함
- frontend, backend, database, webserver를 각각 컨테이너로 사용했음
- frontend는 vue cli로 backend는 node.js, 웹서버는 nginx, db는 mysql 이용
- 아래 docker-compose.yml파일은 내가 사용했던 것. 루트 폴더에 작성

```docker-compose.yml
version: '3'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    volumes:
      - './frontend:/app'
      - '/app/node_modules'
    ports:
      - '8080:8080'

  nginx:
    build:
      dockerfile: Dockerfile
      context: ./nginx 
    ports:
      - '80:80'
    depends_on:
      - frontend

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - '3000:3000'
    volumes:
      - './backend/:/app'
      - '/app/node_modules'

  db:
    image: mysql:5.7 # docker에서 제공하는 이미지 사용
    restart: always
    environment:
      MYSQL_DATABASE: '사용할 DB'
      # Password for root access
      MYSQL_ROOT_PASSWORD: '비밀번호'
    ports:
      # <Port exposed> : < MySQL Port running inside container>
      - '3306:3306'
    expose:
      # Opens port 3306 on the container
      - '3306'
      # Where our data will be persisted
    volumes:
      - my-db:/var/lib/mysql
volumes:
  my-db:
```

#### 1.1 frontend 부분

- `build` 에 있는 `context`는 build를 실행할 경로이고 그 경로에 Dockerfile을 작성해 도커 이미지를 지정
- 아래는 /frontend에 만드는 Dockerfile 예

```dockerfile
FROM node:12.18.2

# set working directory
WORKDIR /app

# add `/app/node_modules/.bin` to $PATH
ENV PATH /app/node_modules/.bin:$PATH

# install and cache app dependencies
COPY package.json /app/package.json
RUN npm install

# start app
CMD npm run serve
```

- `volume` 부분에서`/frontend: /app`은 컨테이너 안의 app directory와 연결시킬 루트폴더의 폴더 의미
- `port`는 사용할 포트 지정

#### 1.2 nginx 부분 

- docker에 있는 nginx 이미지를 사용
- /nginx에 default.conf를 생성

```default.conf
upstream frontend {
    server 내가 사용하는 url or ip:8080;
}
server {
    listen 80;

    location / {
        proxy_pass http://frontend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
    }

}

```

- /nginx 에 있는 Dockerfile

```dockerfile
FROM nginx
COPY ./default.conf /etc/nginx/conf.d/default.conf
```

#### 1.3 backend 부분

- 사용한 Dockerfile

```dockerfile
FROM node:12.18.2
 
#어플리케이션 폴더를 Workdir로 지정
WORKDIR /app
 
#서버 파일 복사 ADD [어플리케이션파일 위치] [컨테이너내부의 어플리케이션 파일위치]
ADD ./ /app
 
#패키지파일들 받기
COPY package.json /app/package.json
RUN npm install
 
#배포버젼으로 설정 - 이 설정으로 환경을 나눌 수 있습니다.
ENV NODE_ENV=production
 
#서버실행
CMD npm start
```

#### 1.4 DB부분

- mysql 이미지 사용

### 2. 실행하기

- `docker-compoer build`를 먼저 한 후에 `docker-compose up`을 한다
- `docker-compose up -d`를 하면 실행 후, 콘솔로 나옴
- 한줄로 바꾸면 `docker-compose up -d --build`

### 해결해야할 문제점

- docker를 사용할 경우 iptables에서 백엔드로 들어오는 것을 drop시킨 것들이 작동 안함. 그래서 누구나 백엔드 접근이 가능해졌음.

- docker를 사용하지 않고 백엔드만 npm start 하면 문제없이 drop시킴
- iptables  DOCKER USER에 추가를 해도 안되고, ufw를 사용해도 다 접근 가능했음
- 조금 더 정확한 정보를 확인해야함