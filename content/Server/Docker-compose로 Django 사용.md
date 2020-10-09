---
title: "Docker-compose로 Django 사용"
tags: ["Django", "Docker-compose", "Gunicorn"]
weight: 5
pre: "<b>5. </b>"
---

# Docker-compose & Django & Gunicorn

>Docker-compose로 Django & Gunicorn 사용하기

- Dockerfile

  ```dockerfile
  FROM python:3.6.9
  ENV PYTHONUNBUFFERED 1
  RUN mkdir /backend
  WORKDIR /backend
  ADD requirements.txt /backend/
  RUN pip install --upgrade pip
  RUN pip install -r requirements.txt
  ADD . /backend/
  ```
  
- docker-compose.yml

  ```docker-compose.yml
  backend:
      build: 
        context: ./backend
        dockerfile: Dockerfile
      restart: always
      env_file:
        - ./backend/.env
      expose:
        - "8001"
      volumes:
        - './backend:/backend'
      command:
        "gunicorn --workers=2 --bind=0.0.0.0:8001 backend.wsgi:application"
  ```

  - 내부 접속만 열기위한 설정

  ```docker-compose.yml
  backend-dev:
      build: 
        context: ./backend
        dockerfile: Dockerfile
      restart: always
      env_file:
        - ./backend/.env
      ports:
        - "8000:8000"
      volumes:
        - './backend:/backend'
      command:
        "gunicorn --workers=2 --bind=0.0.0.0:8000 backend.wsgi:application"
  ```

  - 포트 8000은 외부 접속을 할 수 있도록 하여 개발 중 서버에 있는 DB를 사용

- `expose`를 사용하여 컨테이너 내부의 포트만 사용해 컨테이너 내부 IP 172.xxxxx로 서버에서만 접속 가능하도록 했음

- 명령어 `docker inspect <컨테이너 id>` 로 내부 IP 를 확인 후 `vue.config.js`에서 `proxy`를 사용하여 프록시 설정까지 해줌.

  - 사용 예)

  ```js
  module.exports = {
    devServer: {
      disableHostCheck: true,
      proxy: {
        '/api' : {
          target: 'http://172.19.0.4:8001',
  .......
  ```

  - 컨테이너 중지 후 다시 킬 경우 내부 IP가 변동될 수 있기 때문에 확인하여 수정하거나 내부 IP를 고정시키는 방법을 봐야함.

