---
title: Protocol Laboratory
description: HTTP, HTTPS, SMTP, SMTPS, FTP 프로토콜들을 분석하고 테스트 해보기 위한 컨테이너 기반의 테스트 환경
author: hyeonsu-choe
date: 2024-07-28 15:11 +0900
last_modified_at: 2025-10-26 14:45 +0900
categories: [Projects, Linux]
tags: [personal, linux, docker, container, http, https, smtp, smtps, ftp]
toc: true
pin: false
render_with_liquid: false
image:
  path: /assets/img/projects/protocol_laboratory/proto_lab_structure.png
  # lqip: 
  alt: A container-based testing environment
---

| 구분 | 내용 |
|:----|:----|
|플랫폼|Linux|
|개발 도구 및 사용 언어|Shell script, Golang, Docker|
|개발 기간| 2024년 3월 ~ 2024년 6월 |
|저장소| [바로 가기](https://github.com/hyeonsu-choe/protocol-laboratory)|


## 1. 개요

HTTP, HTTPS, SMTP, SMTPS, FTP와 같은 다양한 프로토콜들을 분석하고 테스트 하기 위한 컨테이거 기반의 테스트 환경이다.  

프로토콜 별로 컨테이너들을 생성하여 단일 서버와 단일 클라이언트들 간 1:1 통신과 레플리카 기능을 활용한 다수의 클라이언트와 단일 서버 간 통신 테스트를 지원한다.  

사용자가 직접 작성한 스크립트를 활용해 테스트를 수행 할 수 있는 기능도 제공한다.    


### 1.1. 운영 환경

---

![vmware configuration](/assets/img/projects/protocol_laboratory/proto_lab_test_env.png)

VMware Workstation 에서 Linux(Ubuntu 22.04) 가 설치 되어 있고, Host-Only 네트워크 어댑터 속성을 사용하는 Virtual Machine 2개를 구성하여 Vmware Workstation 내 Virtual Switch 를 거쳐 통신 되도록 구성된 환경에서 운영 된다.  

또한 Docker 환경이 구성되어 있어야 한다.  

- Server  
`docker-compose` 또는 `docker-swarm` 을 활용하여 서버 속성의 컨테이너들을 관리한다. 

- Client  
`docekr-compose` 를 활용하여 클라이언트 속성의 컨테이너들을 관리한다.  


### 1.2. 전체 구조 및 구성

---

![docker swarm based configuration](/assets/img/projects/protocol_laboratory/proto_lab_structure.png)

위 구조는 서버 측이 `docker-swarm` 모드로 동작 중일 때의 구성도이다.  
그러나 굳이 서버 측을 `docker-swarm` 을 사용해 구동 시킬 필요는 없다.  
기본 컨테이너 실행 명령 또는 `docker-compose`를 활용하여 구동시킬 수 있도록 해놓았기 때문이다.  

다만, `docker-swarm`에서 컨테이너들을 실행하는 경우, `Health check` 를 통해 컨테이너가 비정상 종료 시의 복원, 설정 변경 시 스택 배포를 통한 롤백 업데이트 기능들을 활용할 수 있게 되므로 가능하면 `docker-swarm` 을 이용해 서버 컨테이너들을 운영하는 것을 권장 한다.  

필요한 경우, macvlan 또는 host 네트워크를 사용하여 컨테이너들을 생성하면 된다.  

### 1.2.1. 컨테이너 소개

컨테이너는 서버 측과 클라이언트 측으로 구분 된다.   

#### 서버 측 컨테이너  

|container|port| description|
|:--------|:-----:|:---------|
|http_server| 80 | Golang 기반의 자작 웹서버 http 모드로 구동|
|https_server| 443 | Golang 기반의 자작 웹서버 https 모드로 구동|
|h2c_server| 8080 | `nghttpd` 를 사용하여 http/2 Cleartext 서버 구동|
|ftp_server| 20, 21, 30000-30010| `vsftpd`를 사용하여 ftp 서버 구동|
|smtp_server| 25 | `postfix`를 사용하여 smtp 서버 구동|
|smtps_server| 465 | `postfix`를 사용하여 smtps 서버 구동|


#### 클라이언트 측 컨테이너

|container|description|
|:--------|:---------|
|http1_client| `curl`을 이용해 http/1.1 POST 메시지 전송|
|https1_client| `curl`을 이용해 https로 http/1.1 POST 메시지 전송|
|http2_client| `curl`을 이용해 http로 http/2 POST 메시지 전송|
|https2_client| `curl`을 이용해 https로 http/2 POST 메시지 전송|
|ftp_client| `vsftpd`를 이용해 ftp로 passive 모드 PUT 요청|
|smtp_client| `mailutils`을 이용해 smtp 메일 전송|
|smtps_client| `curl`을 이용해 smtps 메일 전송 |



### 1.2.2. 디렉토리 구조
```bash
protocol-laboratory
│
├── client
│   ├── configure.sh    # 클라이언트 환경 변수 일괄 설정 스크립트 (서버 접속 정보, 테스트 스크립트 반복 횟수, 반복 인터벌 설정)
│   ├── file_client # 프로토콜 별 컨테이너 생성 정보 파일 관리
│   │   ├── build   # docker build 를 통해 상위 디렉토리의 이름을 갖는 도커 이미지를 생성할 수 있음
│   │   └── ftp
│   ├── mail_client # mail_client 이미지 도커 빌드 및 smtp, smtps 컨테이너 실행 파일들 존재
│   │   ├── build
│   │   ├── smtp    # 프로토콜 별 docker-compose 파일과 컨테이너 환경 변수 설정 파일, 테스트 스크립트 파일들 존재
│   │   └── smtps
│   └── web_client
│       ├── build
│       ├── http1
│       ├── http2
│       ├── https1
│       └── https2
└── server
    ├── configure.sh
    ├── file_server
    │   ├── build
    │   └── ftp
    ├── mail_server
    │   ├── build
    │   ├── smtp
    │   └── smtps
    └── web_server
        ├── build
        ├── h2c
        ├── http
        └── https
```

## 2. 도커 이미지 빌드

현재 이 컨테이너 기반 테스트 환경에서 사용되는 이미지 파일들을 `Docker Hub` 등에서 배포하고 있지 않기 때문에 이들을 사용하기 위해서는 이미지를 직접 빌드해야 한다.   

server와 client 디렉토리 하위에서, 빌드하고자 하는 프로토콜 별 컨테이너들 생성 정보 파일 관리 디렉토리 하위에 위치한 `build` 디렉토리리 이동한다.  

여기에서 `docker build` 를 수행하면 된다.  

> docker build -t <도커 이미지 태그명> . --network host  
```bash
cd ./server/web_server/build
docker build -t web_server . --network host
```

⚠️ 이미지 태그명은 docker-compose.yml 파일에 정의된 이미지 파일 이름 또는 상위 디렉토리 이름을 따르도록 한다.  



## 3. 실행 방법

### 3.1. 서버 측

---

### 3.1.1. `configure.sh` 실행 

---

`protocol-laboratory/server` 에 있는 `configure.sh` 를 이용하여 실행할 컨테이너들의 환경 변수 파일 내 값들을 변경 한다.  

> ./configure.sh <서버 호스트 IP>  
``` bash
cd ./server
./configure.sh 10.10.10.10
```

#### 서버 측 컨테이너 별 환경 변수

생성될 각 컨테이너들이 사용할 환경 변수들은 다음과 같이 각 컨테이너 생성 파일 경로에 위치한 환경 변수 파일 내에서 통합 관리 된다. 
> `./server/web_server/https/config/env/https_server.env`   

|구분 |도커 이미지명| 환경 변수 이름| 허용 값| 설명 | 환경 변수 파일 위치| 
|:------|:------:|:-----------------:|:------|:------|:------:|
|https 서버|web_server|`RUN_MODE`|`https` <br> `http`|서버 구동 모드 (`http` / `https`)|`./server/web_server/https/config/env/https_server.env`|
|h2c 서버|h2c_server|`RUN_MODE` |`h2c` <br> `h2`|서버 구동 모드 (`h2`/`h2c`)|`./server/web_server/https/config/env/https_server.env`|
|h2c 서버|h2c_server|`SERVER_PORT` |정수 값|포트 번호 |`./server/web_server/https/config/env/https_server.env`|
|ftp 서버|file_server|`USER_ID`|ID 값 |FTP 서버 사용자 계정 ID|`./server/file_server/ftp/config/env/ftp_server.env`|
|ftp 서버|file_server|`USER_PASS`|Password 값 |FTP 서버 사용자 계정 Password|`./server/file_server/ftp/config/env/ftp_server.env`|
|ftp 서버|file_server|`SERVER_ADDR`|Relay 서버 IP 주소 |FTP 서버가 구동 될 호스트의 IP 주소|`./server/file_server/ftp/config/env/ftp_server.env`|
|smtp 서버<br>smtps 서버|mail_server|`RUN_MODE`|`smtps` <br> `smtp`| 서버 구동 모드 (`smtp` / `smtps`)|`/server/mail_server/smtp/config/env/smtp_server.env`<br>`/server/mail_server/smtps/config/env/smtps_server.env`|
|smtp 서버<br>smtps 서버|mail_server|`SERVER_DOMAIN`|hello@`my.test.com` 류의 도메인 문자열 <br> or 목적지 서버 IP 주소|메일 서버가 사용 할 도메인|`/server/mail_server/smtp/config/env/smtp_server.env`<br>`/server/mail_server/smtps/config/env/smtps_server.env`|
|smtp 서버<br>smtps 서버|mail_server|`RECEIVER`|`root`| 메일 서버 수신자 계정명|`/server/mail_server/smtp/config/env/smtp_server.env`<br>`/server/mail_server/smtps/config/env/smtps_server.env`|



### 3.1.2. docker run 명령을 통해 실행

---

직접 수동 명령을 통해 실행하는 방법이다.   

각 컨테이너 별로 사용되는 환경 변수들이 다르기 때문에 서버 측 환경 변수 이름과 값들을 참고해서 사용해야 한다.    


#### 컨테이너 생성 및 실행

> docker run -d --name <생성 할 컨테이너 이름> -e <환경 변수 이름>="<환경 변수 값>" <도커 이미지명> 
```bash
docker run -d --name https-server -p 443:443 -e RUN_MODE="https" 192.168.83.9:30443/test/web_server    # https 서버
```

#### 컨테이너 실행 종료
```bash
docker rm -f https-server
```


### 3.1.3. docker-compose 클라이언트를 통해 실행

---

가장 자주 사용되는 실행 방법으로써, 

`docker run` 명령 보다 편하게 컨테이너들을 관리하는 방법이다.  

각 컨테이너들의 환경 변수 값들은 해당 설정 디렉토리에서 별도의 환경 변수 파일들을 통해 관리 된다.  

컨테이너들은 디렉토리의 depth에 따라 실행 및 생성 시키는 프로토콜 별 컨테이너들의 범위가 달라진다.  

예를 들어서, `./server` 위치에서 `docker-compose` 를 실행하면 `web_server`, `file_server`, `mail_server` 가 모두 한 번에 생성 및 실행된다.  

만약, `./server/web_serevr/http` 위치에서 `docker-compose`를 실행하면 `web_server` 즉 `https` 서버와 `http` 서버 2개가 실행되는 방식인 것이다.  

💡 이 기능을 활용하기 위해서는 `docker-compose` 클라이언트가 설치되어 있어야 한다.

#### 컨테이너들 생성 및 실행

```bash
cd ./ftp_server
docker-compose up -d
```

#### 컨테이너들 실행 종료
```bash
cd ./ftp_server
docker-compose down
```

### 3.1.4. Docker Swarm 모드를 통해 실행

---
docker swarm 모드로 실행 여부는 선택 사항이다.

컨테이너들을 `docker run` 명령을 통해 직접 실행 시킬 수도 있고, `docker-compose` 클라이언트 도구를 통해 보다 간편하게 실행 시킬 수도 있기 때문이다.  

그러나 `docker swarm`을 사용하면, `stack.yml` 파일에 명세해 놓은 health check 명령을 이용해 docker swarm의 자동 원복 기능을 활용할 수 있게 된다.  

이것을 활용하게 되면 컨테이너에서 문제가 발생 하였을 때, `docker swarm` 의 매니저 노드가 서비스(컨테이너)를 알아서 원복시켜 준다.  


#### 도커 스웜 모드 활성화

> docker swarm init --advertise-addr <인터페이스 이름>
```bash
docker swarm init --advertise-addr ens4
```

#### 도커 스웜 상태 확인
```bash
docker node ls
ID                            HOSTNAME                 STATUS    AVAILABILITY   MANAGER STATUS   ENGINE VERSION
m74fwxq0x5diof2rdsi3709km *   server-virtual-machine   Ready     Active         Leader           27.4.1
```

#### 도커 스웜 배포
```bash
cd ./server
docker stack deploy -c stack.yml test_server
```

> 💡`docker-compose.yml` 파일을 사용하여 배포할 수도 있다. 이 경우 포트에 IPv4 주소만 할당해야 한다.

#### 도커 스웜 서비스 종료
```bash
cd ./server
docker stack rm test_server
```

### 3.2. 클라이언트 측

---

### 3.2.1. `configure.sh` 실행 

---

`protocol-laboratory/client` 에 있는 `configure.sh` 를 이용하여 실행할 컨테이너들의 환경 변수 파일 내 값들을 변경 한다.  

> ./configure.sh <서버 호스트 IP> <테스트 스크립트 LOOP 카운트> <테스트 스크립트 LOOP 시 초단위의 INTERVAL 값>  
``` bash
cd ./client
./configure.sh 10.10.10.10 0    # 모든 프로토콜 별 컨테이너들이 접속 할 서버의 주소를 설정하고 테스트 스크립트의 loop 횟수를 0(무한)으로 설정
./configure.sh 10.10.10.10 5 1   # 스크립트의 loop 횟수를 5회, loop 당 간격은 1초로 설정
```

#### 클라이언트 측 컨테이너 별 환경 변수

|구분 |도커 이미지명| 환경 변수 이름| 허용 값| 설명 | 환경 변수 파일 위치| 
|:------|:------:|:-----------------:|:------|:------|:------:|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`RUN_MODE`|`https` <br> `http`|TLS 사용 여부 모드|`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/http2/config/env/http2_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`HTTP_VERSION`|`1.1` <br> `2`|HTTP 버전|`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/http2/config/env/http2_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`TLS_VERSION`|`1.0`<br>`1.1`<br>`1.2`<br>`1.3`|TLS 버전|`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`SERVER_ADDR`|서버 IP 주소 ||`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`SERVER_DOMAIN`| 서버 도메인 문자열 ||`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/http2/config/env/http2_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`SERVER_PORT`|정수 값|서버 포트 번호|`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/http2/config/env/http2_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`TEST_LOOP_COUNT`| 정수 값 |테스트 스크립트 파일 전체 실행 횟수<br>0 이면 무한 루프|`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/http2/config/env/http2_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|http1.1 클라이언트<br>https1.1 클라이언트<br>http2 클라이언트<br>https2 클라이언트|web_client|`TEST_LOOP_INTERVAL_SEC`| 정수 값 |테스트 스크립트 전체 반복 수행 간격 (초 단위)|`./client/web_client/http1/config/env/http1_client.env`<br>`./client/web_client/https1/config/env/https1_client.env`<br>`./client/web_client/http2/config/env/http2_client.env`<br>`./client/web_client/https2/config/env/https2_client.env`|
|ftp 클라이언트|file_client|`SERVER_ADDR`|서버 IP 주소||`./client/file_client/ftp/config/env/ftp_client.env`|
|ftp 클라이언트|file_client|`USER_ID`|ID 값 |FTP 서버 사용자 계정 ID|`./client/file_client/ftp/config/env/ftp_client.env`|
|ftp 클라이언트|file_client|`USER_PASS`|Password 값 |FTP 서버 사용자 계정 Password|`./client/file_client/ftp/config/env/ftp_client.env`|
|ftp 클라이언트|file_client|`TEST_LOOP_COUNT`| 정수 값 |테스트 스크립트 파일 전체 실행 횟수<br>0 이면 무한 루프|`./client/file_client/ftp/config/env/ftp_client.env`|
|ftp 클라이언트|file_client|`TEST_LOOP_INTERVAL_SEC`| 정수 값 |테스트 스크립트 전체 반복 수행 간격 (초 단위)|`./client/file_client/ftp/config/env/ftp_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`RUN_MODE`|`smtps` <br> `smtp`| TLS 사용 여부 |`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`SERVER_DOMAIN`|hello@`my.test.com` 류의 도메인 문자열 <br> or 목적지 서버 IP 주소|메일 서버 도메인|`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`RECEIVER`|`root`| 메일 서버 수신자|`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`SERVER_ADDR`|서버 IP 주소|`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`SERVER_PORT`|정수 값|서버 포트 번호|`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`TEST_LOOP_COUNT`| 정수 값 |테스트 스크립트 파일 전체 실행 횟수<br>0 이면 무한 루프|`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|
|smtp 서버<br>smtps 서버|mail_client|`TEST_LOOP_INTERVAL_SEC`| 정수 값 |테스트 스크립트 전체 반복 수행 간격 (초 단위)|`/client/mail_client/smtp/config/env/smtp_client.env`<br>`/server/mail_client/smtps/config/env/smtps_client.env`|



### 3.2.2. docker run 명령으로 직접 실행

---

서버 측의 사용법과 동일하다.   

각 컨테이너 별로 사용되는 환경 변수들이 다르기 때문에 서버 측 환경 변수 이름과 값들을 참고해서 사용해야 한다.    

#### 컨테이너 생성 및 실행

> docker run -d --name <생성 할 컨테이너 이름> -e <환경 변수 이름>="<환경 변수 값>" <도커 이미지명> 
```bash
docker run -d --name https-server -p 443:443 -e RUN_MODE="https" 192.168.83.9:30443/test/web_server    # https 서버
```

#### 컨테이너 실행 종료
```bash
docker rm -f https-server
```

### 3.2.3. docker-compose 클라이언트를 이용하여 실행

서버 측의 사용법과 동일하다.  

#### 컨테이너들 생성 및 실행

```bash
cd ./ftp_server
docker-compose up -d
```

#### 컨테이너들 실행 종료
```bash
cd ./ftp_server
docker-compose down
```

### 3.2.4. replica 를 이용한 다수 개의 컨테이너 생성 및 실행

클라이언트의 경우 `docker-compose.yml` 파일에서 `replicas` 설정을 통해 생성되는 컨테이너 개수를 제어할 수 있다.  

이를 위해서는 `docker-compose.yml` 파일 내에 주석 처리 되어 있는 설정 항목들을 해제하고 자신의 환경에 맞춰 값을 수정하면 된다.  

```bash
services:
    https1-client:
        image: web_client
        env_file:
            - ./config/env/https1_client.env
        volumes:
            - ./config/cert:/app/config/cert
#        networks:
#            - macvlan_net
#        deploy:
#            mode : replicated
#            replicas: 10
#            restart_policy:
#                condition: on-failure

# 서로 다른 IP를 갖는 다수의 클라이언트들이 요구 될 때 사용
#networks:
#    macvlan_net:
#        driver: macvlan
#        driver_opts:
#            parent: ens32 # 클라이언트 측 인터페이스 지정
#        ipam:
#            driver: default
#            config:
#                - subnet: 10.10.10.0/24 # 사용 할 ip 대역
#                  gateway: 10.10.10.1

```

## 4. 커스텀 테스트 설정

기본적으로 각 컨테이너들은 이미지에 내장된 테스트 스크립트가 실행된다.

![client loop test script](/assets/img/projects/protocol_laboratory/container_inner_flow.png)

그러나 클라이언트 측 컨테이너들의 경우에는 설정된 환경 변수 값에 따라 loop를 돌며 테스트 스크립트들을 실행하는 단순한 구조를 갖는다.  

그리고 이 테스트 환경은 `docker-compose-custom.yml` 파일을 머지하여 실행 시키는 방법을 통해 사용자가 직접 만든 테스트 스크립트와 파일들을 사용하여 테스트 할 수 있도록 한다.    


### 4.1. 바인드 마운트

---

`docker-compose-custom.yml` 파일은 호스트의 디렉토리를 컨테이너에 마운트 하기 위한 디렉토리 경로가 설정 되어 있다.  

그리고 다음의 이 두 디렉토리를 바인드 마운트 함으로써 테스트에 사용 할 스크립트와 파일들을 실시간으로 교체 할 수 있다.  

- `./config/test_scripts`
컨테이너 내 `/app/test_scripts/running` 경로로 마운트 된다.
loop를 돌며 실행 시킬 테스트 스크립트들이 보관 되어있는 경로


- `./config/files`
컨테이너 내 `/app/files` 경로로 마운트 된다.   
업로드/다운로드 될 파일들이 보관 되어있는 경로

### 4.2. 테스트 스크립트

---

테스트 스크립트는 각 컨테이너에서 사용되는 환경 변수 이름들의 도움을 받아 자유롭게 작성하면 된다.  

```bash
#!/bin/bash

curl -F upload_file=@./files/test.dat http://$SERVER_DOMAIN:$SERVER_PORT/files/test.dat
```

⚠️ 테스트 스크립트 내에서 무한 루프를 구성하면 다른 테스트 스크립트들이 실행되지 못하게 되니 주의해야 한다.  


### 4.3. 실행 방법

---
클라이언트 측 각 프로토콜 컨테이너 설정 파일에 위치한 `./config/test_scripts` 와 `./config/files` 경로에 반복 수행 할 테스트 스크립트와 해당 스크립트에 의해 다루어질 파일들을 먼저 복사해 놓은 뒤 실행을 해야 원하는 결과를 얻을 수 있다.

💡 `docker-compose-custom.yml` 파일은 client 디렉토리 내 각 depth 별로 배치되어 있다.  

#### 실행
```bash
cd ./client/web_client
docker-compose -f ./docker-compose.yml -f ./docker-compose-custom.yml up -d
```

#### 종료
```bash
docker-compose -f ./docker-compose.yml -f ./docker-compose-custom.yml down
```