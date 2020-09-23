---
title: "많이 사용한 Ubuntu / Linux 명령어"
tags: ["Ubuntu"]
weight: 2
pre: "<b>2. </b>"
---

# 명령어

>나중에 생각안날때 보려고 만든 Ubuntu 18.04 에서 배포시 많이 사용했던 명령어들

- `netstat -nlpt`  현재 listen 중인 포트 확인 `netstat -nat`로도 확인 가능
- `netstat -nap | grep 포트 번호` 특정 포트 확인
- `fuser -k -n tcp 포트번호` 포트번호에서 사용중인 프로세스 죽이기
- `vi`로 파일을 읽고 수정했을 때 `:wq`저장후 닫기 `:qa` 저장안하고 닫기 `:qa!` qa안될때
- `docker ps` 현재 켜져있는 docker 확인
- `docker stop`  뒤에 `docker ps`  `으로 감싸면 실행중인 docker 파일들 종료
- `docker-compose build --no-cache` 캐시 이용 안하고 build
- `iptables -L --line-numbers` iptable 룰 번호 확인
- `iptables -D chain이름  숫자` 위에서 확인한 번호 룰 삭제할 때
- `iptables -I chain이름 -p tcp -s 허용할 ip --dport 포트번호 -j ACCEPT` 지정한 포트 번호 특정 IP 허용 맨 윗줄에 룰 삽입

# SSH 접속 느릴 때

- `vi /etc/ssh/sshd_config`로 sshd_config 파일 수정
  - `GSSAPIAuthentication no` 주석 해제
  - `UseDNS no`주석 해제
  - `$ service sshd restart` 로 재시작