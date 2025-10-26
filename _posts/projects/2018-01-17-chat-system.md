---
title: Chat System
description: Windows 환경에서 C++, Win32 API를 활용하여 만든 채팅 시스템
author: hyeonsu-choe
date: 2018-01-17 19:46 +0900
last_modified_at: 2025-10-26 22:05 +0900
categories: [Projects, Windows]
tags: [personal, win32, odbc, cpp, iocp, winsock, socket, network, sql, database]
toc: true
pin: false
render_with_liquid: false
image:
  path: /assets/img/projects/chat_system/server_structure.png
  # lqip: 
  alt: Structure of chat system
---

| 구분 | 내용 |
|:----|:----|
|플랫폼|Windows|
|개발 도구 및 사용 언어|MS Visual Studio 2010, C++, Winsock(IOCP), MS-SQL Server 2012|
|개발 기간| 2016년 7월 ~ 2017년 6월 |
|저장소| [바로 가기](https://github.com/hyeonsu-choe/chat-suite)|


## 1. 프로그램 소개

![네트워크 시스템 구성 및 메시지의 흐름](/assets/img/projects/chat_system/server_structure.png)

본 시스템은 Windows의 Console 모드에서 작동하는 채팅 프로그램으로써, 클라이언트 사용자가 로비에 접속하여 채팅방을 개설하거나 개설된 채팅방에 입장하여 다른 접속자들과 대화를 주고 받는 기본적이고도 단순한 채팅 기능들을 제공한다.

프로그램은 전체적으로 `Main Server` + `Client` + `Log Server` + `DB Server` 로 구성되어져 있으며 이들은 정해진 응용 프로토콜에 따라 메시지를 주고 받으며 하나의 시스템으로서 동작한다.

기본적으로 각 개체간 양방향 통신을 하지만 예외적으로 Main Server와 Log Server는 단방향 통신을 한다.


DB의 경우, DBMS 모드와 File System 모드를 모두 지원한다.


기본적으로는 Windows의 ODBC를 통해 Main Server와 DB가 연동하는 방식으로 동작한다.

그러나 만약 DB와 연결을 할 수 없는 경우, File System 모드로 변환되어 동작한다.


채팅 서버의 실행 모습  
![서버 실행 모습](/assets/img/projects/chat_system/server_running.png)  

채팅 클라이언트의 실행 모습  
![클라이언트의 실행 모습](/assets/img/projects/chat_system/client_running.png)  

로그 서버의 실행 모습  
![로그 서버의 실행 모습](/assets/img/projects/chat_system/log_server_running.png)  

로그 파일의 모습  
![로그 파일의 모습](/assets/img/projects/chat_system/log_file_screenshot.png)  
  

### 지원 기능

---

다음은 프로그램 전체에 걸쳐 구현 되어져 있는 기능들이다.


| 번호 | 기능 설명 |
|:----:|:----------------------------------------------|
| 1 | DB(DBMS(MS-SQL) or File System)를 통한 계정 정보 관리 |
| 2 | 일반 사용자 계정과 관리자 계정의 분리 |
| 3 | 채팅방과 Lobby의 분리 |
| 4 | 사용자의 대화명 변경 (서로 다른 사용자 간 중복 사용 O) |
| 5 | 개설된 채팅방에 입장/퇴장 |
| 6 | 공개/비공개 채팅방 개설 |
| 7 | 개설된 채팅방 삭제 (방장 권한 보유 시) |
| 8 | 개설된 방 제목 변경 (방장 권한 보유 시) |
| 9 | 개설된 방 입장 비밀번호 변경 (방장 권한 보유 시) |
| 10 | Host 권한 넘겨주기 (방장 권한 보유 시) |
| 11 | 특정 유저를 채팅방에서 추방 (방장 권한 보유 시) |
| 12 | 접속자 명단 확인 |
| 13 | 개설된 방 목록 확인 |
| 14 | 명령어를 제외한 다른 모든 문자들의 대/소문자 구분 |
| 15 | 특정 IP의 서버 접속 차단 및 해제 (관리자 계정 전용, File System 모드에서는 영구 저장 X) |
| 16 | 특정 계정(ID)의 로그인 차단 및 해제 (관리자 계정 전용, File System 모드에서는 영구 저장 X) |
| 17 | 특정 클라이언트 원격 종료 (관리자 계정 전용) |
| 18 | 접속자들의 접속 이후의 모든 행위를 Log Server로 전송하여 기록 및 보관 |


## 2. 일반 사용자와 관리자 계정의 구분

일반 사용자 채팅 기능과 관리자 고유의 기능을 별도로 제공하기 위해 계정을 일반 사용자 계정과 관리자 계정으로 그 속성을 나누어 제공한다.

이들 계정 속성은 DB 내 UserCode 값으로 구분 되어진다.

UserCode 값이 0~9인 계정은 관리자 계정이며, 그 외에는 모두 일반 사용자 계정으로 구분된다.

이 UserCode 값은 최초 로그인 시 계정 생성 때 자동으로 0 부터 증가 및 할당 되는 방식으로 생성 되어진다.

만약 수동으로 수정을 원하는 경우,

DBMS의 Account 테이블 또는 Filesystem의 Account.dat 파일에서 직접 수정하면 된다.

이는 개발 과정에서 편의를 위해 이렇게 구현했던 것인데, 아쉽게도 관리자 계정 생성 명령 및 처리 기능 까지는 구현하지 못하였다.


| 구분 | 설명 |
|------|------|
| 계정 구분 | 일반 사용자 계정과 관리자 계정으로 속성을 나누어 제공 |
| 구분 기준 | DB 내 **UserCode** 값으로 구분 |
| 관리자 계정 | UserCode 값이 `0~9` 인 계정 |
| 일반 사용자 계정 | UserCode 값이 `10 이상` 인 계정 |
| UserCode 생성 방식 | 최초 로그인 시 계정 생성 때 **0부터 자동 증가 및 할당** |
| 수동 수정 방법 | - DBMS의 **Account 테이블** <br> - Filesystem의 **Account.dat** 파일 직접 수정 |
| 제한 사항 | 관리자 계정 생성 명령 및 처리 기능은 구현되지 않음 |


## 3. 명령어 모음

명령어는 일반 사용자들의 명령어와 관리자 고유 명령어로 구성된다.

### 일반 사용자 명령어

---

유저 코드가 10 이상인 유저들이 사용하는 명령어다.

모든 명령어는 대/소문자를 가리지 않고 입력이 가능하다.

그러나 명령어 뒤에 선택적으로 입력되는 입력값들의 경우에는 대/소문자가 구분되며 공백 입력 또한 허용 된다.

예를 들어, 대화방 이름, 대화방 비밀 번호, 대화명 모두 대/소문자가 구분되며 공백 입력이 허용 된다.

예외적으로, 계정 ID와 계정 Password는 대/소문자가 구분되며, 공백 문자 입력 또한 허용 되지 않는다.

1\) 로그인 / 계정 생성 : `[Account ID]/[Account Password]`

![로그인 및 계정 생성 명령 입력 화면](/assets/img/projects/chat_system/lgn_screen1.png)  

- 서버에 접속하기 위해서는, "계정명"을 입력하고, 스페이스 바 키로 한칸 띄운 뒤에 "계정 비밀번호"를 연속 입력 하고 엔터키를 눌러야 한다.
- 입력된 ID가 DB에 등록되어 있지 않으면 자동으로 신규 계정으로 등록, 생성 되어진 뒤에 대화명 설정 단계로 진입 한다.
- 계정 ID와 Password의 경우 영문자 입력시 최소 6문자~ 최대 12문자, 한글 입력시 최소 3문자~최대 6문자 까지만 허용 된다.  
  
2\) 채팅방 목록 조회 : `/LIST`

![채팅방 목록 조회 화면](/assets/img/projects/chat_system/list.png)  

- 현재 서버내에 개설되어 있는 대화방의 목록을 보여준다.


3\) 접속자 목록 조회 : `/GUEST`

![접속자 목록 조회 화면](/assets/img/projects/chat_system/guest.png)  

- 현재 대화방(Lobby 포함)의 정보와  접속해 있는 유저들의 목록을 보여준다.
  

4\) 대화방 생성 : `/CR [대화방 이름] [#대화방 비밀 번호]`

![대화방 생성 화면](/assets/img/projects/chat_system/cr.png)  

- 서버에게 대화방을 하나 새로 생성해 줄것을 요청 한다.
- 명령어 입력시, 대화방 이름 또는 입장 비밀 번호 생략이 가능하다.
- 대화방 이름 생략시 제목 없음이라는 이름이 자동 부여 되어 생성 된다.
- 입장 비밀 번호 생략시 "공개 대화방"이, 지정 시에는 "비공개 대화방"이 생성 된다.
- 방을 생성한 유저는 대화방 생성과 동시에 Host 권한을 얻는다.

[공개 대화방 생성]
 ex) "/cr" or "/cr 내가 만든 방"

[비공개 대화방 생성] :
 ex) "/cr #123 45" or "/cr 내가 만든 방 #123 45"


5\) 대화방 삭제 : `/DR [대화방 비밀 번호]`

![대화방 삭제 화면](/assets/img/projects/chat_system/dr.png)  

- 서버에게 현재 위치한 대화방을 삭제해 달라고 요청 한다.
- 만약 삭제 대상이 공개방일 경우 입장 비밀 번호 입력이 필요하지 않다.
- 만약 삭제 대상이 비공개방일 경우 입장 비밀 번호를 추가로 입력해야만 한다.


6\) 대화방에 입장 :  `/JOIN [입장 할 대화방의 제목] #[대화방 비밀 번호]` or `/JN [입장 할 대화방의 번호] [대화방 비밀 번호]`

![대화방 입장 화면](/assets/img/projects/chat_system/join.png)  
![대화방 입장 화면](/assets/img/projects/chat_system/jn.png)  

- 공개방일 경우에는 입장 대화방의 번호만 입력 한다.
- 비공개방일 경우에는 대화방 비밀 번호를 함께 입력해야 한다.

7\) 대화방에서 퇴장 : `/LV`

![대화방 퇴장 화면](/assets/img/projects/chat_system/lv.png)  

- 현재 위치한 방에서 퇴장하여 Lobby로 이동한다.
- 만약 현재 위치가 Lobby라면, Login 화면으로 이동 한다.
- 만약 명령어 사용 대상이 대화방의 Host라면, Host 권한은 다른 User에게 자동으로 넘어간다.

8\) 채팅방에서 추방 : `/K [추방 대상의 대화명]`

![대화방 추방 화면](/assets/img/projects/chat_system/k.png)  

- 대화방에서 사용자를 추방 한다.
- Host 권한을 가지고 있어야만 사용 가능하다.
- 대상이 현재 대화방 내에 위치해 있어야만 한다.

9\)  방장 권한 넘기기 : `/GH [권한을 넘겨줄 대상의 대화명]`

![대화방 권한 넘기기 화면](/assets/img/projects/chat_system/gh.png)  

- 현재 자신이 Host의 권한을 지정한 대상에게 넘겨 준다.
- Host 권한을 가지고 있어야만 사용 가능하다.
- 대상이 현재 대화방 내에 위치해 있어야만 한다.

10\) 방 이름 변경 : `/CHGT [생성 할 대화방 제목]`

![대화방 이름 변경 화면](/assets/img/projects/chat_system/cght.png)  

- 현재 대화방의 이름을 변경 한다.
- 다른 대화방의 이름과는 중복을 허용하지 않는다.

11\) 방 비밀번호 변경 : `/CHGP [생성 할 대화방의 비밀 번호]`

![대화방 비밀번호 변경 화면](/assets/img/projects/chat_system/chgp.png)  

- 현재 대화방의 입장 비밀 번호를 변경 한다.
- 만약 현재 대화방이 공개방이었던 경우 비공개방으로 자동 전환 된다.
- 한 번 비공개방으로 변경되면 다시 공개방으로 변경 할 수 없다.

12\) 대화명 변경 : `/CHGN [변경 할 대화명]`

![대화 변경 화면](/assets/img/projects/chat_system/chgn.png)  

- 현재의 대화명을 다른 대화명으로 변경 한다.
- 대화명은 서버내 모든 사용자들이 중복 사용 가능 하다.

13\) 도움말 보기 : `/H`

![도움말 보기 화면](/assets/img/projects/chat_system/h.png)  

- 프로그램 실행 중 어느 위치에서건 사용이 가능하다.

14\) 프로그램 종료 : `/Q`

- 프로그램을 즉시 종료 한다.
- 만약 명령어 사용자가 Host 권한을 가지고 있었다면 임의로 다른 사용자에게 Host 권한이 넘어간다.
- 만약 대화방의 접속자가 명령어 사용자 한 명 뿐이었다면, 현재의 대화방은 자동으로 삭제 처리 된다.


### 관리자 전용 명령어

---

1\) 특정 계정의 로그인 접속 차단 : `/BAN [대상의 ID]`

![특정 계정 로그인 차단 화면](/assets/img/projects/chat_system/ban.png)  

- 입력된 ID의 계정을 로그인 접속 차단 한다.
- 입력된 ID의 계정이 현재 서버에 접속 중이면 강제 종료 시킨다.


2\) 특정 계정의 로그인 접속 차단 해제 : `/UNBAN [대상의 ID]`

![특정 계정 로그인 차단 해제 화면](/assets/img/projects/chat_system/unban.png)  

- 입력된 ID의 계정의 로그인 접속 차단을 해제 한다.


3\) 특정 IP의 서버 접속 차단 : `/IPBAN [대상의 IP]`

![특정 IP 차단 화면](/assets/img/projects/chat_system/ipban.png)  

- 입력된 IP의 서버 접속을 차단 한다.
- 입력된 IP의 클라이언트가 현재 접속해 있으면 강제 접속 종료 시킨다.


4\) 특정 IP의 서버 접속 차단 해제 : `/UNIPB [대상의 IP]`

![특정 IP 차단 해제 화면](/assets/img/projects/chat_system/unipb.png)  

- 입력된 IP의 서버 접속 차단을 해제한다.


5\) 특정 계정의 원격 프로그램 종료 : `/END [대상의 ID]`

![특정 계정 프로그램 원격 종료 시키 화면](/assets/img/projects/chat_system/end.png)  

- 입력된 ID의 계정을 강제 접속 종료 시킨다


6\) 전체 알림말 전송 : `/N [전송 할 메세지]`

![전체 공지 명령 화면](/assets/img/projects/chat_system/n.png)  

- 서버내 모든 클라이언트들에게 입력한 메세지를 전송 한다


7\) 현재 서버 접속자 수 조회 : `/UCNT`

![현재 접속자 수 조 화면](/assets/img/projects/chat_system/ucnt.png)  

- 서버내 접속자 수를 보여 준다.


8\) 원격 서버 종료 : `/SDOWN`

![원격 서버 종료 화면](/assets/img/projects/chat_system/sdown.png)  

- 접속해 있는 모든 클라이언트들과의 접속을 순차적으로 강제 종료하고 최종적으로 서버프로그램을 종료 한다.
- 클라이언트들이 자체적으로 종료나 로그 아웃을 하지 않는 것이기 때문에 변경된 정보를 따로 DBMS에 저장하지는 않는다.


## 4. 시스템 구조 및 개요

### 전체적인 시스템의 구조

---

![시스템의 네트워크 구조 및 메세지 흐름의 방향](/assets/img/projects/chat_system/Netword_Diagram.png) 

시스템은 Main Server와 Client 그리고 Log Server, DB Server로 나뉘어져 구성되어 있으며 각 개체간 네트워크 상에서 메세지를 주고 받으며 작동 한다.

* Main Server :  실질적인 채팅 서버로써 대표적으로 다음과 같은 일들을 수행한다.
  - 접속 차단 대상의 접속을 승인 및 거부
  - Client의 Login 요청 처리
  - Client가 요청하는 각종 명령어 및 서버내 접속한 Client들 간의 메세지 교환 처리
  - Client가 요청하는 사용자 정보를 DB를 통해 관리
  - Client가 취하는 일련의 행동들을 Log Server로 전송하여 그 행적들을 기록

* Log Server : Main Server에서 보내져 오는 정보를 바탕으로 Log를 작성하여 보조 기억 장치에 저장 한다.

* DB : Server 접속자들의 계정 정보(ID, Password, Nickname, Usercode)들을 관리하며 Windows의 ODBC를 통해 연결된 DBMS(MS-SQL)에 의해 관리된다.

* Client :  사용자가 Main Server로 부터 일련의 서비스들을 받기 위한 목적으로 사용하는 응용 프로그램.

Client는 물리적으로는 계정 정보 입력, 대화명 생성,  서버내에서의 대화라는 3가지 상태를, 논리적으로는 접속자 계정 정보 입력, 대화명 생성, 대기실, 대화방, 종료라는 4가지 상태를 오가며 작동 한다.

전자인 물리적 상태의 경우 응용 프로그램인 Client의 관점에서 바라보는 상태 전이로써, 클라이언트 내부에 이에 필요한 일련의 작업들과 자료구조들이 코드로써 직접적으로 구현되어 있다.

후자인 논리적 상태 의 경우 Client 사용자의 관점에서 바라보는 프로그램의 상태를 말하는 것으로써, 클라이언트의 활동 범위를 서버로 까지 확장한 것으로 클라이언트가 서버상에서구현된 자료 구조에 따라 상태가 전이 되는 것을 통칭하는 것이다

물론 이 경우 클라이언트, 서버 양쪽 모두에 이를 위한 상태 전이 전용 코드가 별도로 존재하지는 않는다.


### 각 파트별 구현

---

#### Application Protocol

#### 1) 서버와 클라이언트의 동작 구조 개요

![서버와 클라이언트 동작 flow](/assets/img/projects/chat_system/serv_client_outline.png) 


서버와 클라이언트는 기본적으로 수신한 메시지 내의 명령 코드를 추출한 뒤, 메시지를 명령 코드에 맞는 처리 함수들에 넘겨 처리하는 방식으로 작동 한다.

예를 들어, 만약 메시지의 명령 코드핃드의 값이 3이라면, 명령코드 3의 처리 함수를 호출하고 앞에서 수신한 메시지를 인자 값으로 넘기는 것이다.

물론 이것들을 수행하는 주체는 IOCP 상에 등록된 각각의 스레드들이다.

사실 위의 그림은 다른 부수적인 과정들이나 여러 세부 사항들을 모두 생략하고 매우 단순하게 도식화한 것으로 실제로는 조금 더 복잡하게 돌아간다.

이에 대한 보다 상세한 내용은 [👉 여기로 이동](#6-수신-메시지-분해-및-처리-과정) 에서 보다 자세하게 다룰 것이다.


#### 2) 메세지의 기본 구조

서버와 클라이언트가 주고 받는 메세지의 기본 포맷은 다음과 같다.

![메시지 기본 구조](/assets/img/projects/chat_system/Msg_Default_Format.png) 

각 칸은 가변적인 Byte 단위로써 구분 되어지며, 공백은 Ascii 값 32(10진수)인 공백 문자로 표현된다.

(1) 메시지 길이 :  
문자 '/'에서 부터 문자열의 끝인 nul(0) 문자 까지의 길이, 이 범위 안에는 문자 '/'과 nul 문자 까지 포함.  
만약 메시지 길이가 30이라면 문자 '3', '0'과 같이 각 1바이트의 2개 문자로 구성 되게 된다.


(2) 명령 코드 :  
클라이언트가 특정 서비스를 받기 위해 서버로 요청할때 쓰이는 필드.  
이 필드에 입력되는 명령 코드들은 "/JOIN"과 같은 사용자 명령어와 대응되는 정수 값들로 구성되어져 있으며 이들은 각각 서버측의 명령어 처리 함수들과 대응되는 관계에 있다.  

만약 이 값이 10이라면 문자 '1', '0'과 같이 각 1바이트의 2개의 문자 값으로 구성 되어 서버측으로 전송된 다음 서버측에서 다시 정수 값으로 변환되게 된다.  
그리고 서버측에서는 이 번호를 참조하여 10번 함수 포인터 테이블에 등록된 함수를 호출, 해당 명령어를 처리한다.  
다음은 사용자 명령어와 그에 대응되는 명령 코드 값들의 목록이다.  

사용자 명령어와 명령 코드  
![사용자 명령어와 명령 코드](/assets/img/projects/chat_system/client_inst_table.png) 

여기에서 "로그인 요청", "닉네임 생성 요청", "대화방 접속자 갱신 요청" 3가지 요청 메세지는 사용자가 직접 입력할 수 없는 일종의 시스템 명령 코드라고 할 수 있다.

이중 "대화방 접속자 갱신 요청" 메시지의 경우 서버측에 대화방 접속자들의 목록을 요청하는 메세지인데 이는 이미 서버로 부터 갱신 받아 클라이언트에 저장중인 대화방 접속자들의 목록을 조회하는 "/GUEST" 명령어와는 다른 별개의 명령어다.

다음은 클라이언트로 부터 받은 요청(명령)을 처리하고 그 결과를 클라이언트에게 돌려주는 등의 클라이언트와의 상호 작용을 위한 서버측의 명령어와 그 코드들이다.

서버측의 대클라이언트 명령어와 명령 코드  
![서버측의 대클라이언트 명령어와 명령 코드](/assets/img/projects/chat_system/server_inst_table.png) 

(3) 옵션n :  
명령 코드의 종류에 따라 선택적, 또는 필수적으로 추가 사용 되어지는 필드로써 명령어 종류에 따라 크기가 달라지는 가변적인 크기(Byte 단위)를 갖는다.

그러나 메시지를 조립 및 분해하는 과정에서는 이 옵션 필드의 개수에 상관 없이 옵션 필드 전체를 하나의 옵션 필드로 상정하고 다룬다.

"일반 메세지 전송" 메시지의 포맷 : 1개의 옵션 필드만 존재  
![일반 메세지 전송" 메시지의 포맷](/assets/img/projects/chat_system/normal_msg_format.png) 

"일반 메세지 전송" 메시지 사용의 예  
![일반 메세지 전송 메시지 사용예](/assets/img/projects/chat_system/normal_msg_ex1.png) 

"대화방 생성 명령" 메시지의 포맷 : 4개의 옵션 필드가 존재  
![대화방 생성 명령 메시지의 포맷](/assets/img/projects/chat_system/cr_format.png) 

"대화방 생성 명령" 메시지 사용의 예  
![대화방 생성 명령 메시지 사용예](/assets/img/projects/chat_system/cr_ex1.png) 

#### 3) Client의 서비스 요청(명령어) 메시지의 구조와 메시지 처리 함수

(1) 일반 메세지 전송 : "/0"

"일반 메시지 전송" 메시지의 포맷  
![일반 메시지 전송 메시지 포맷 화면](/assets/img/projects/chat_system/normal_msg_format.png) 

일반 메시지 전송  
![일반 메시지 전송 메시지](/assets/img/projects/chat_system/normal_msg_ex1.png) 

메시지 처리 함수 : `bool NormalMessage(CParam *p_pArg)`  


(2) 대화방 입장 1 : "/JN"

"대화방 입장 명령 /JN" 메시지의 포맷  
![대화방 입장 명령 메시지 포맷](/assets/img/projects/chat_system/jn_format.png) 

대화방 번호와 비밀번호 모두를 입력한 경우  
![대화방 번호와 비밀번호 모두 입력 케이스](/assets/img/projects/chat_system/jn_ex2.png) 

대화방 번호만 입력한 경우  
![대화방 번호만 입](/assets/img/projects/chat_system/jn_ex1.png) 

메시지 처리 함수 : `bool JoinRoom(CParam *p_pArg)`


(3) 대화방 입장 2 : "/JOIN"


"대화방 입장 명령 /JOIN" 메시지의 포맷  
![3번 포맷](/assets/img/projects/chat_system/cr.png) 

대화방명과 비밀번호 모두를 입력한 경우  
![3번 케이스](/assets/img/projects/chat_system/join_ex1.png) 

대화방명만 입력한 경우  
![3번 케이스](/assets/img/projects/chat_system/join_ex2.png) 

메시지 처리 함수 : `bool JoinRoomWithTitle(CParam *p_pArg)`


(4) 퇴장 : "/LV"

"퇴장 명령" 메시지의 포맷  
![4번 명령1](/assets/img/projects/chat_system/lv_format.png) 

퇴장  
![4번 명령1](/assets/img/projects/chat_system/lv_ex.png) 

메시지 처리 함수 : `bool LeaveRoom(CParam *p_pArg)`


(5) 대화방 생성 : "/CR"

"대화방 생성 명령" 메시지의 포맷  
![5번 명령1](/assets/img/projects/chat_system/cr_format.png) 

대화방명, 비밀번호 모두 입력한 경우  
![5번 명령2](/assets/img/projects/chat_system/cr_ex1.png) 

대화방명만 입력한 경우  
![5번 명령3](/assets/img/projects/chat_system/cr_no_pass.png) 

비밀번호만 입력한 경우  
![5번 명령4](/assets/img/projects/chat_system/cr_no_title.png) 

아무것도 입력하지 않은 경우  
![5번 명령5](/assets/img/projects/chat_system/cr_no_title_no_pass.png) 

메시지 처리 함수 : `bool CreateRoom(CParam *p_pArg)`

(6) 대화방 삭제 : "/DR"

"대화방 삭제 명령" 메시지의 포맷  
![6번 명령1](/assets/img/projects/chat_system/dr_format.png) 

비밀번호를 입력한 경우  
![6번 명령2](/assets/img/projects/chat_system/dr_ex2.png) 

아무것도 입력하지 않은 경우  
![6번 명령3](/assets/img/projects/chat_system/dr_ex1.png) 

메시지 처리 함수 : `bool DestroyRoom(CParam *p_pArg)`

(7) 추방 : "/K"

"추방 명령" 메시지의 포맷  
![7번 명령1](/assets/img/projects/chat_system/k_format.png) 

추방  
![7번 명령2](/assets/img/projects/chat_system/k_ex.png) 

메시지 처리 함수 : `bool KickOut(CParam *p_pArg)`


(8) 대화방명 변경 : "/CHGT"

"대화방명 변경 명령" 메시지의 포맷  
![8번 명령1](/assets/img/projects/chat_system/chgt_format.png) 

대화방명 변경  
![8번 명령2](/assets/img/projects/chat_system/chgt_ex.png) 

메시지 처리 함수 : `bool ChangeRoomTitle(CParam *p_pArg)`


(9) 대화방 비밀번호 변경 : "/CHGP"

"대화방 비밀번호 변경 명령" 메시지의 포맷  
![9번 명령1](/assets/img/projects/chat_system/chgp_format.png) 

대화방명 변경  
![9번 명령2](/assets/img/projects/chat_system/chgp_ex.png) 
메시지 처리 함수 : `bool ChangeRoomPass(CParam *p_pArg)`


(10) 로그인 요청 :

"로그인 요청" 메시지의 포맷  
![10번 명령1](/assets/img/projects/chat_system/login_format.png) 

로그인 요청  
![10번 명령2](/assets/img/projects/chat_system/login_ex.png) 

메시지 처리 함수 : `bool LoginCheck(CParam *p_pArg)`


(11) 닉네임 변경 : "/CHGN"

"닉네임 변경 명령" 메시지의 포맷  
![11번 명령1](/assets/img/projects/chat_system/chgn_format.png) 

닉네임 변경  
![11번 명령2](/assets/img/projects/chat_system/chgn_ex.png) 
메시지 처리 함수 : `bool ChangeNickName(CParam *p_pArg)`


(12) 방장 권한 넘기기 : "/GH"

"방장 권한 넘겨주기 명령" 메시지의 포맷  
![12번 명령1](/assets/img/projects/chat_system/k_format.png) 

방장 권한 넘겨주기  
![12번 명령2](/assets/img/projects/chat_system/gh_ex.png) 
메시지 처리 함수 : `bool GiveHostship(CParam *p_pArg)`


(13) 닉네임 생성 요청 : "/12"

"닉네임 생성 요청" 메시지의 포맷  
![13번 명령1](/assets/img/projects/chat_system/chgn_format.png) 

닉네임 생성 요청  
![13번 명령2](/assets/img/projects/chat_system/create_nick_ex.png) 

메시지 처리 함수 : `bool CreateNickName(CParam *p_pArg)`


(14) 대화방 목록 갱신 요청 : "/LIST"

"대화방 목록 갱신 요청" 메시지의 포맷  
![14번 명령1](/assets/img/projects/chat_system/lv_format.png) 


대화방 목록 갱신 요청  
![14번 명령2](/assets/img/projects/chat_system/list_ex.png) 

메시지 처리 함수 : `bool UpdateRoomList(CParam *p_pArg)`


(15) 대화방 접속자 목록 갱신 요청:

"대화방 접속자 목록 갱신 요청" 메시지의 포맷  
![15번 명령1](/assets/img/projects/chat_system/lv_format.png) 


대화방 접속자 목록 갱신 요청  
![15번 명령2](/assets/img/projects/chat_system/update_guest_ex.png) 

메시지 처리 함수 : `bool UpdateGuestList(CParam *p_pArg)`


(16) 서버 강제 종료 : "/SDOWN"

"서버 강제 종료" 메시지의 포맷  
![16번 명령1](/assets/img/projects/chat_system/lv_format.png) 

서버 강제 종료  
![16번 명령2](/assets/img/projects/chat_system/sdown_ex.png) 

메시지 처리 함수 : `bool ShutdownServer(CParam *p_pArg)`


(17) 특정 계정 로그인 차단 : "/BAN"

"특정 계정의 로그인 차단 명령" 메시지의 포맷  
![17번 명령1](/assets/img/projects/chat_system/ban_format.png) 

특정 계정의 로그인 차단  
![17번 명령2](/assets/img/projects/chat_system/ban_ex.png) 

메시지 처리 함수 : `bool Ban(CParam *p_pArg)`


(18) 특정 계정 로그인 차단 해제 : "/UNBAN"

"특정 계정의 로그인 차단 해제 명령" 메시지의 포맷  
![18번 명령1](/assets/img/projects/chat_system/ban_format.png) 

특정 계정의 로그인 차단 해제  
![18번 명령2](/assets/img/projects/chat_system/unban_ex.png) 

메시지 처리 함수 : `bool UnBan(CParam *p_pArg)`


(19) 특정 계정의 클라이언트를 원격 종료 : "/END"

"특정 계정이 사용중인 클라이언트를 원격 종료 명령" 메시지의 포맷  
![19번 명령1](/assets/img/projects/chat_system/ban_format.png) 

특정 계정이 사용중인 클라이언트를 원격 종료  
![19번 명령2](/assets/img/projects/chat_system/end_ex.png) 

메시지 처리 함수 : `bool ForcedToDisconnect(CParam *p_pArg)`


(20) 특정 IP의 서버 접속 차단 : "/IPBAN"

"특정 IP의 서버 접속 차단 명령" 메시지의 포맷  
![20번 명령1](/assets/img/projects/chat_system/ipban_format.png) 

특정 IP의 서버 접속 차단  
![20번 명령2](/assets/img/projects/chat_system/ipban_ex.png) 

메시지 처리 함수 : `bool IPBan(CParam *p_pArg)`


(21) 특정 IP의 서버 접속 차단 해제 : "/UNIPB"

"특정 IP의 서버 접속 차단 해제 명령" 메시지의 포맷  
![21번 명령1](/assets/img/projects/chat_system/ipban_format.png) 

특정 IP의 서버 접속 차단 해제  
![21번 명령2](/assets/img/projects/chat_system/unipban_ex.png) 

메시지 처리 함수 : `bool UnIPBan(CParam *p_pArg)`


(22) 전체 알림말 전송 : "/N"

"전체 알림말 전송" 메시지의 포맷  
![22번 명령1](/assets/img/projects/chat_system/normal_msg_format.png) 

전체 알림말
메시지 처리 함수 : `bool NotifyMessage(CParam *p_pArg)`

(23) 서버 전체 접속자수 조회 : "/UCNT"

메시지 구조  
![23번 명령1](/assets/img/projects/chat_system/lv_format.png) 

서버 전체 접속자수 조회  
![23번 명령2](/assets/img/projects/chat_system/ucnt_ex.png) 

메시지 처리 함수 : `bool CountPeople(CParam *p_pArg)`

#### 4) Server의 Client에 대한 메시지의 구조(Client 관점에서의 수신 메시지)와 메시지 처리 함수

(1) 일반 메시지 전송 : NormalMsg
(2) 시스템 메시지 전송 : SystemMsg
(3) 알림 메시지 전송 : NoticeMsg


메시지 구조  
![server의client에 대한 메시지 구조 1번 명령1](/assets/img/projects/chat_system/normal_msg_format.png) 

사용예 : "Hello~!!" 메시지 전송  
![server의client에 대한 메시지 구조 1번 명령2](/assets/img/projects/chat_system/nm.png) 

사용예 : "System!!" 메시지 전송  
![server의client에 대한 메시지 구조 1번 명령3](/assets/img/projects/chat_system/sysmsg.png) 

사용예 : "Notice!!" 메시지 전송  
![server의client에 대한 메시지 구조 1번 명령4](/assets/img/projects/chat_system/noticemsg.png) 

메시지 처리 함수 :  `void OutputMsg(TCHAR *p_pMsg)`


(4) 로그인 성공 알림 : SuccessfulLogin

메시지 구조  
![server의client에 대한 메시지 구조 4번 명령1](/assets/img/projects/chat_system/successfullogin_format.png) 

사용예 : 클라이언트에게 로그인 성공 소식과 함께 해당 계정의 대화명 "Choi"와 Usercode "12"를 전송  
![server의client에 대한 메시지 구조 4번 명령2](/assets/img/projects/chat_system/successfulLogin_ex.png) 

메시지 처리 함수 : `void ChangeNickname(TCHAR *p_pMsg)`, `void CMyClient::ChangeState(CClntState* p_pClntState)`

(5) 로그인 실패 알림 : LoginFailed

메시지 구조  
![server의client에 대한 메시지 구조 5번 명령1](/assets/img/projects/chat_system/normal_msg_format.png) 

사용예 : 클라이언트에게 로그인 실패 정보와 실패 이유를 덧 붙여 알림
![server의client에 대한 메시지 구조 5번 명령2](/assets/img/projects/chat_system/loginfailed_ex.png) 

메시지 처리 함수 : `void OutputMsg(TCHAR *p_pMsg)`


(6) 로그인 모드로의 이행 : GoToLoginMode
(7) 닉네임 생성 모드로의 이행 : GoToCrtNickMode

메시지 구조  
![server의client에 대한 메시지 구조 6-7번 명령1](/assets/img/projects/chat_system/lv_format.png) 

사용예 : 클라이언트로 하여금 로그인 모드로 이행할 것을 지시  
![server의client에 대한 메시지 구조 6-7번 명령2](/assets/img/projects/chat_system/gotologin_ex.png) 

사용예 : 클라이언트로 하여금 닉네임 생성 모드로 이행할 것을 지시  
![server의client에 대한 메시지 구조 6-7번 명령3](/assets/img/projects/chat_system/gotocrtnick_ex.png) 

메시지 처리 함수 : `void CMyClient::ChangeState(CClntState* p_pClntState)`


(8) Client 내부의 사용자 닉네임 정보 갱신 : UpdateNickName

메시지 구조  
![server의client에 대한 메시지 구조 8번 명령1](/assets/img/projects/chat_system/updatenickname_format.png) 

사용예 : Client로 하여금 현재 가지고 있는 사용자의 대화명 정보를 "Choi"로 갱신할 것을 지시  
![server의client에 대한 메시지 구조 8번 명령2](/assets/img/projects/chat_system/updatenickname_ex.png) 

메시지 처리 함수 : `void ChangeNickname(TCHAR *p_pMsg)`


(9) 대화방 정보를 대화방 목록에 추가 : AppendRoom

메시지 구조  
![server의client에 대한 메시지 구조 9번 명령1](/assets/img/projects/chat_system/appendroom_format.png) 

사용예 : 공개방 생성  
![server의client에 대한 메시지 구조 9번 명령2](/assets/img/projects/chat_system/append_room_ex1.png) 

사용예 :비공개방 생성  
![server의client에 대한 메시지 구조 9번 명령3](/assets/img/projects/chat_system/append_room_ex2.png) 

메시지 처리 함수 : `void AppendRoomToList(TCHAR *p_pMsg)`


(10) 대화방 목록 초기화 : ClearRoomList

메시지 구조  
![server의client에 대한 메시지 구조 10번 명령1](/assets/img/projects/chat_system/lv_format.png) 

대화방 목록 초기화  
![server의client에 대한 메시지 구조 10번 명령2](/assets/img/projects/chat_system/clear_room_ex.png) 

메시지 처리 함수 : `CRoomList::Destroy()`


(11) 대화방 목록 출력 : ShowRoomList

메시지 구조  
![server의client에 대한 메시지 구조 11번 명령1](/assets/img/projects/chat_system/lv_format.png) 

대화방 목록 출력  
![server의client에 대한 메시지 구조 11번 명령2](/assets/img/projects/chat_system/show_room_ex.png) 

메시지 처리 함수 : `void OutputRoomList()`


(12) 대화방 목록 갱신 지시 : PrepareToUpdateRoomList

메시지 구조  
![server의client에 대한 메시지 구조 12번 명령1](/assets/img/projects/chat_system/lv_format.png) 


대화방 목록 갱신 지시  
![server의client에 대한 메시지 구조 12번 명령2](/assets/img/projects/chat_system/prepare_to_update_roomlist_ex.png) 


메시지 처리 함수 : `SendOne(p_hSock, UpdateGuestList, NULL)`
부가 설명 :

(13) 대화방 접속자 목록 초기화 : ClearGuestList

메시지 구조  
![server의client에 대한 메시지 구조 13번 명령1](/assets/img/projects/chat_system/lv_format.png) 

대화방 접속자 목록 초기화  
![server의client에 대한 메시지 구조 13번 명령2](/assets/img/projects/chat_system/clear_guest_list_ex.png) 


메시지 처리 함수 : `CGuestList::Destroy()`


(14) 접속자 정보를 대화방 접속자 목록에 추가 : AppendGuest

메시지 구조  
![server의client에 대한 메시지 구조 14번 명령1](/assets/img/projects/chat_system/append_guest_format.png) 

사용예 : 호스트 권한이 없는 접속자 정보를 접속자 목록에 추가  
![server의client에 대한 메시지 구조 14번 명령2](/assets/img/projects/chat_system/append_guest_ex1.png) 

사용예 : 호스트 권한이 있는 접속자 정보를 접속자 목록에 추가  
![server의client에 대한 메시지 구조 14번 명령3](/assets/img/projects/chat_system/append_guest_ex2.png) 

메시지 처리 함수 : `void AppendGuestToList(TCHAR *p_pMsg)`


(15) 접속자 정보를 대화방 접속자 목록에서 제거 : RemoveGuest

메시지 구조  
![server의client에 대한 메시지 구조 15번 명령1](/assets/img/projects/chat_system/remove_guest_format.png) 

유저코드 12의 접속자를 접속자 목록에서 제거  
![server의client에 대한 메시지 구조 15번 명령2](/assets/img/projects/chat_system/remove_guest_ex.png) 

메시지 처리 함수 : `void RemoveGuestFromList(TCHAR *p_pMsg)`


(16) 대화방 접속자 목록 갱신 준비 : PrepareToUpdateGuestList

메시지 구조  
![server의client에 대한 메시지 구조 16번 명령1](/assets/img/projects/chat_system/lv_format.png) 

대화방 접속자 목록 갱신 지시  
![server의client에 대한 메시지 구조 16번 명령2](/assets/img/projects/chat_system/prepare_to_update_guestlist_ex.png) 

메시지 처리 함수 : `SendOne(p_hSock, UpdateGuestList, NULL)`
부가 설명 :


(17) 대화방 접속자 대화명 갱신 : UpdateGuestNickName

메시지 구조  
![server의client에 대한 메시지 구조 17번 명령1](/assets/img/projects/chat_system/update_guest_nickname_format.png) 

사용예 : 유저코드 값이 12인 접속자의 대화명을 Choi로 변경  
![server의client에 대한 메시지 구조 17번 명령2](/assets/img/projects/chat_system/update_guest_nickname_ex.png) 

메시지 처리 함수 : `void ModifyGuestNicknameOnList(TCHAR *p_pMsg)`


(18) 대화방 방장 권한 정보 갱신 : UpdateHostShip

메시지 구조  
![server의client에 대한 메시지 구조 18번 명령1](/assets/img/projects/chat_system/remove_guest_format.png) 

사용예 : 유저코드 값이 12인 접속자를 대화방의 방장으로 설정  
![server의client에 대한 메시지 구조 18번 명령2](/assets/img/projects/chat_system/update_hostship_ex.png) 

메시지 처리 함수 : `void ModifyHostOnList(TCHAR *p_pMsg)`


(19) 대화방 목록상에서의 대화방명 갱신 : UpdateRoomTitleOnRoomList

"대화방명 변경 명령" 메시지의 포맷  
![server의client에 대한 메시지 구조 19번 명령1](/assets/img/projects/chat_system/chgt_format.png) 

사용예 : 클라이언트에 저장되어 있는 대화방의 제목을 "New Title"로 갱신  
![server의client에 대한 메시지 구조 19번 명령2](/assets/img/projects/chat_system/update_roomtitle_ex.png) 

메시지 처리 함수 : `void CCurrentRoom::SetTitle(TCHAR* p_pTitle)`


(20) 대화방 속성 정보를 (공개/비공개)로 변경 : SetRoomAttribute

메시지 구조  
![server의client에 대한 메시지 구조 20번 명령1](/assets/img/projects/chat_system/set_room_attribute_format.png) 

사용예 : 클라이언트에 저장되어 있는 현재 접속중인 대화방의 속성을 공개방으로 갱신  
![server의client에 대한 메시지 구조 20번 명령2](/assets/img/projects/chat_system/set_room_attribute_ex1.png) 

사용예 : 클라이언트에 저장되어 있는 현재 접속중인 대화방의 속성을 비공개방으로 갱신  
![server의client에 대한 메시지 구조 20번 명령3](/assets/img/projects/chat_system/set_room_attribute_ex2.png) 

메시지 처리 함수 : `void CCurrentRoom::SetPrivate(bool p_bPrivate)`


(21) 현재 머물고 있는 대화방의 정보를 갱신 : UpdateRoomInfo

메시지 구조  
![server의client에 대한 메시지 구조 21번 명령1](/assets/img/projects/chat_system/appendroom_format.png) 

사용예 : 클라이언트에 저장되어 있는 대화방 정보를 해당 정보(방번호 3, 방제목 My Room, 방장 Choi, 공개방)로 갱신 또는 추가  
![server의client에 대한 메시지 구조 21번 명령2](/assets/img/projects/chat_system/update_room_info_ex1.png) 

사용예 : 클라이언트에 저장되어 있는 대화방 정보를 해당 정보(방번호 7, 방제목 Come In, 방장 Lee, 비공개방)로 갱신 또는 추가  
![server의client에 대한 메시지 구조 21번 명령3](/assets/img/projects/chat_system/update_room_info_ex2.png) 

메시지 처리 함수 : `void StoreRoomInformation(TCHAR *p_pMsg)`


(22) 퇴장 완료 : LeaveCompleted

메시지 구조  
![server의client에 대한 메시지 구조 22번 명령1](/assets/img/projects/chat_system/lv_format.png) 

퇴장 완료  
![server의client에 대한 메시지 구조 22번 명령2](/assets/img/projects/chat_system/leave_completed_ex.png) 

메시지 처리 함수 : `SendOne(p_hSock, JoinRoom, Buf)`
부가 설명 :

(23) 퇴장 요청 : RequestLeave

메시지 구조  
![server의client에 대한 메시지 구조 23번 명령1](/assets/img/projects/chat_system/lv_format.png) 

퇴장 요청  
![server의client에 대한 메시지 구조 23번 명령2](/assets/img/projects/chat_system/request_leave_ex.png) 

메시지 처리 함수 : `SendOne(p_hSock, LeaveRoom, NULL)`
부가 설명 :

(24) EOF 메시지 전송 요청 : RequestEOFMessage

메시지 구조  
![server의client에 대한 메시지 구조 24번 명령1](/assets/img/projects/chat_system/lv_format.png) 

서버로의 EOF메시지 전송을 요청  
![server의client에 대한 메시지 구조 24번 명령2](/assets/img/projects/chat_system/request_eof_msg_ex.png) 

메시지 처리 함수 : `CloseHandling(p_hSock, 0)`

#### 5) 명령어 포맷 구성 및 해체 작업 지원 함수들

(1) int ExtractMsgLen(TCHAR *str) : 메시지의 최선두에 기록되어 있는 유효 문자열 길이 값을 추출하는 함수

a) 반환값 : 유효 문자열 길이

b) str : 메시지

![명령어 포맷 구성 및 해체 작업 지원 함수 1 스크린샷1](/assets/img/projects/chat_system/extractmsglen.png) 


(2) int ExtractIndex(TCHAR *str) : 메시지에서 명령 번호 추출

a) 반환값 : 명령 번호

b) str : 메시지

![명령어 포맷 구성 및 해체 작업 지원 함수 2 스크린샷1](/assets/img/projects/chat_system/extractindex.png) 


(3) 자릿수 추출 함수 : int CntDigit(int p_nNum)

a) 반환 값 : p_nNum의 자릿수

b) p_nNum : 자릿수를 알아내고자 하는 정수값

![명령어 포맷 구성 및 해체 작업 지원 함수 3 스크린샷1](/assets/img/projects/chat_system/cntdigit.png) 


(4) int OffsetFor(TCHAR *pBuf, TCHAR Delim) : Delim으로 지정된 문자값 까지의 offset 값 계산

a) 반환값 : offset값

b) pBuf : 메시지

c) Delim : offset 값을 구할 대상 문자

![명령어 포맷 구성 및 해체 작업 지원 함수 4 스크린샷1](/assets/img/projects/chat_system/offsetfor.png) 


(5) int AsmMsg(TCHAR *p_pDst, int p_nInstNum, TCHAR *p_pMsg) : 메시지 조립 함수

a) 반환 값 : 최종적으로 전송할 메시지의 길이(byte 단위)

b) p_pDst : 조립된 메시지를 저장할 버퍼

c) p_nInstNum : 명령 코드

d) p_pMsg : 전송할 메시지 본문(옵션 필드 값들의 집합)

![명령어 포맷 구성 및 해체 작업 지원 함수 5 스크린샷1](/assets/img/projects/chat_system/asmmsg.png) 

#### 6) 수신 메시지 분해 및 처리 과정

수신한 메시지를 분해하고 처리하는 일련의 과정들은 위의 지원 함수들의 토대 위에서 이루어지며 이 과정을 간략하게 도식화 하면 다음과 같다.

![수신 메시지 분해 및 처리 과정 스크린샷1](/assets/img/projects/chat_system/serv_client_outline2.png) 


이는 1) 서버와 클라이언트의 동작 구조 개요 에 있는 서버와 클라이언트의 개괄적인 메시지 처리 과정을 조금더 세분화한 것이다.

물론 이 또한 실제 구현된 것과는 다르게 개괄적인 흐름을 보다 이해하기 쉽게 단계적으로 표현한 도식이다.

이 순서도에는 Queue가 등장하는데 이는 연결지향형 소켓 특성상 한 번에 여러 메시지가 수신 버퍼에 수신되는 일이 발생 했을때 이들을 개별적인 단위의 메시지들로 구분 및 분해한 뒤에, Queue에 넣고 하나씩 Dequeue 해가며 처리하기 위한 용도로 사용하기 위한 것이다.

다만 실제 구현에서는 위 순서도 처럼 Queue에 각 Message들 전체가 모두 저장되는 것이 아니라, 수신 버퍼내의 각 Message들의 시작 위치의 offset 값이 저장 된다.

또한 수신 버퍼 크기로 인해 소켓의 입력 버퍼에서 모두 읽어오지 못한 불완전 Message들을 처리 하기 위한 일련의 과정들이 추가 되어 있다.

다음은 실제 구현 코드의 순서도이다.

![수신 메시지 분해 및 처리 과정 스크린샷2](/assets/img/projects/chat_system/recv_flow.png) 


동작 원리는 다음과 같다.

Socket으로 부터 수신할 버퍼(Socket의 버퍼가 아니다)의 크기가 45 bytes이고 Socket에 다음과 같이 4개의 메시지들이 한 번에 수신된 경우,

![수신 메시지 분해 및 처리 과정 스크린샷3](/assets/img/projects/chat_system/normal_msg_ex1.png)  
![수신 메시지 분해 및 처리 과정 스크린샷4](/assets/img/projects/chat_system/chgn_ex.png)  
![수신 메시지 분해 및 처리 과정 스크린샷5](/assets/img/projects/chat_system/noticemsg.png)  
![수신 메시지 분해 및 처리 과정 스크린샷6](/assets/img/projects/chat_system/update_guest_ex.png)  


최초의 수신 버퍼의 모습은 다음과 같을 것이다.

![수신 메시지 분해 및 처리 과정 스크린샷6](/assets/img/projects/chat_system/recv_buf1_1.png) 


이제 메시지의 헤더에 있는 40 값과 이 40의 자릿수 2를 더하여 메시지 전체의 길이 값 42를 구한다.

42는 수신 버퍼의 최대 길이 값인 45 보다 작으므로 이 메시지는 잘린 메시지가 아닌 것으로 간주한다.

그리고 이 첫번째 메시지의 시작 주소, 즉 수신 버퍼 내에서의 offset 값인 0을 Msg Header Queue에 Enqueue 한다.

Msg Header Queue  
![수신 메시지 분해 및 처리 과정 스크린샷7](/assets/img/projects/chat_system/Msg_Header_Queue1.png) 


첫번째 메시지의 길이 값 42는 수신 버퍼의 유효 수신 데이터 길이인 45 보다 작으므로 아직 첫번째 메시지 뒤에 두번째 메시지가 존재 하는 것으로 보고 42값을 offset으로 하여 두번째 메시지로 이동 한다.


![수신 메시지 분해 및 처리 과정 스크린샷8](/assets/img/projects/chat_system/recv_buf1_2.png) 

첫번째 메시지와 같은 방식으로 두번째 메시지의 길이 값을 구한다.

이 값은 13이다.

이 값을 첫번째 메세지 길이인 42와 더하면 55가 된다.

그런데 55는 수신 버퍼의 최대 길이 값인 45를 초과한다.

이런 경우 이 초과한 offset의 위치에 있는 메시지는 잘린 메시지로 간주한다.

여기서 잘린 메시지란 아직 그 메시지의 일부를 Socket 버퍼에 남겨 놓고 있는 불완전한 메시지를 뜻한다.

즉, 두번째 메시지는 잘린 (불완전한)메시지으로 그 나머지 데이터들은 아직 소켓의 버퍼에 남아 있는 것으로 간주하는 것이다.


잘린 메시지가 존재하므로 메시지들을 길이 값을 토대로 구분하여 Queue에 넣는 작업은 여기서 중단한다.

이어서 Queue에 넣어 두었던 offset 값들(이 경우 0 하나만 들어 있다)을 차례대로 Dequeue 하면서 이를 참조하여 각 메시지들(이 경우 Hello~ 메시지 한 개)을 대응되는 루틴으로 넘겨 처리 한다.

Queue에 있던 메시지들을 모두 Dequeue 하여 처리하고 나면 Queue는 당연히 아래와 같이 텅 비게 된다.

![수신 메시지 분해 및 처리 과정 스크린샷9](/assets/img/projects/chat_system/Msg_Header_Queue1_2.png) 


그리고 아직 수신 버퍼에 후미에 남아 있는 잘린 (불완전한) 메시지들을 수신 버퍼 가장 앞으로 옮겨오는 작업을 수행 한다.

![수신 메시지 분해 및 처리 과정 스크린샷10](/assets/img/projects/chat_system/recv_buf1_3.png) 

이는 다음 recv 함수 호출때 잘린 나머지 메시지들을 Socket 버퍼로 부터 이어서 수신받기 위한 사전 작업이다.


따라서 두번째 recv 함수 호출 직후의 버퍼의 모습은 다음과 같게 된다.

![수신 메시지 분해 및 처리 과정 스크린샷11](/assets/img/projects/chat_system/recv_buf2_1.png) 


이후의 작업은 위에서 했던 작업의 반복이다.

메시지의 최선두에서 첫번째 메시지의 길이 값인 13을 추출하고 이를 버퍼 최대 길이와 비교한다.

13은 45 보다 작으므로 잘린 메시지가 없는 것으로 간주하고 이 첫 번째 메시지의 시작 offset 값 0을 Queue에 넣는다.

그리고 메시지의 길이 값 13은 수신 버퍼의 유효 수신 데이터 길이인 32 보다 작으므로 두번째 메시지가 존재한다는 것을 알게 되었다.

offset 값 0에 13을 더해 두번째 메시지로 넘어간다.

![수신 메시지 분해 및 처리 과정 스크린샷12](/assets/img/projects/chat_system/recv_buf2_2.png) 


두번째 메시지의 최선두에서 두번째 메시 길이 값이 14임을 추출해내고 첫번째 메시지의 길이 값인 13과 합산하여 수신 버퍼의 최대 길이와 비교한다.

27은 44보다 작으므로 잘린 메시지는 없는 것으로 간주하고 이 두번째 메시지의 시작 offset 값인 13을 Queue에 넣는다.

첫번째와 두번째 메시지 길이의 총합 27은 버퍼내 유효 수신 데이터 길이인 32 보다 작으므로 기존의 offset 값인 13에 두번째 메시지의 길이인 14를 더하여 세번째 메시지의 시작 offset인 27로 넘어간다.

![수신 메시지 분해 및 처리 과정 스크린샷13](/assets/img/projects/chat_system/recv_buf2_3.png) 


세번째 메시지의 길이 값은 5이고, 첫번째와 두번째 그리고 세번째 메시지 까지의 길이의 총합은 32다.

이 값은 버퍼의 최대 길이 보다 작으므로 잘린 메시지는 없는 것으로 간주하고 세번째 메시지의 시작 offset 값을 Queue에 넣는다.

다음으로 이 32 값을 수신 버퍼의 유효 수신 데이터의 길이인 32와 비교를 한다.

두 값은 같으므로 뒤에 더이상 검색할 메시지가 남아 있지 않다고 판단하고 Queue에서 차례대로 각 메시지들의 시작 주소 값을 뽑는 작업으로 넘어간다. 

![수신 메시지 분해 및 처리 과정 스크린샷13](/assets/img/projects/chat_system/Msg_Header_Queue2.png) 

위와 같이 큐에 보관된 메시지들을 하나씩 꺼내어 해당 명령 코드 별로 대응되는 각 메소드(명령어 처리 함수)들에게로 넘겨 처리한다.


메시지의 조립 과정은 주로 Client에서 이루어지고 있는데 이 과정이라고 해봐야 단순히 Application Protocol에 맞추어 메시지별로 필요한 작업들을 수행하는 것이기 때문에 다루지 않았다.


#### Main Server

1\) 서버 상에서의 클라이언트의 상태 전이

서버 접속자(Client)의 명령어 입력 따른 서버 상에서의 상태 전이
![Main Server 서버 상에서의 클라이언트의 상태 전이 스크린샷 1](/assets/img/projects/chat_system/chat_server_diagram.png) 

Client의 사용자는 서버에 접속한 직후 부터 각종 명령어들을 사용하여 접속자 계정 정보 입력, 대화명 생성, 대기실, 대화방, 종료라는 5가지의 논리적 상태를 오가게 된다.

예를들어, 처음 서버에 접속하면 사용자의 계정과 비밀번호를 인증하는 접속자 계정 정보 입력 창이 뜨며, 인증을 성공하면 대기실에 입장하게 된다.

여기서 대화방을 생성하는 "/CR" 명령어나 이미 만들어진 대화방에 입장하는 "/JOIN"과 같은 명령어들을 통해 대화방에 입장, 다른 접속자들과 메세지를 주고 받을 수 있게 된다.

물론 위의 상태 전이는 클라이언트 사용자의 관점에서 바라본 논리적 상태 전이를 의미하는 것이기 때문에 서버상에 이 상태 전이 자체만을 위해 구현된 코드는 따로 존재하지 않는다.

단순히 클라이언트의 정보를 Lobby나 Chat Room에 복사해 넣거나 갱신, 삭제하는 작업들을 수행하여 클라이언트가 위치한 장소에 변화를 줄 뿐이지만, 서버에 접속한 클라이언트의 입장에서는 마치 클라이언트 프로그램이 위의 도표 처럼 서버 내에서 상태 전이를 하고 있는 것 처럼 보이게 된다.


(1) 접속자 계정 정보 입력(Login Mode) : 채팅 서버에 입장하기 위해 계정 ID와 Password 입력을 통해 인증을 요구하는 상태
(2) 대화명 생성 : 대화방에서 사용할 대화명이 지정되지 않은 경우 이를 지정하기 위한 상태
(3) 대기실(Lobby) : 대화방에 입장 하기 전에 대기하는 장소로서 채팅은 물론 대화방 생성이 가능한 상태
(4) 대화방(Chat Room) : 본격적으로 제한된 공간에서 다른 접속자들과 대화를 할 수 있는 상태
(5) 종료 : 프로그램을 종료

서버는 서버에 접속한 다수의 클라이언트들이 위와 같은 상태 전이의 모습을 보일 수 있게끔 서버내 자료구조 상에서 각 클라이언트들의 위치를 통제하며 클라이언트들 상호간 메세지를 교환해 주는 역할을 주로 수행한다.


2\) 구현 클래스 다이어그램  
![구현 클래스 다이어그램](/assets/img/projects/chat_system/chat_server_UML.png) 


3\) 주요 객체들의 자료 구조

(1) CParam : 서버측의 명령어 처리 함수들의 파라미터로 쓰인다.

a) TCHAR *m_pBuffer : 하나의 유효 메시지 단위로 나누어져 있는 각 메시지들의 시작 주소에 대한 포인터 변수.
참고로 이 메시지들이 들어 있는 버퍼는 클라이언트 소켓의 수신 버퍼다.

b) int m_nLen : 클라이언트 소켓의 수신 버퍼에 수신된 전체 메시지들의 길이
이것은 일반 메시지 전송 등 필요한 곳에서 쓰인다.

c) CClntInfo *m_pClntInfo : 클라이언트의 정보(닉네임, 유저 코드, 소켓에 대한 핸들 등)를 가지고 있는 일종의 버퍼 객체에 대한 포인터 변수.


각 노드들의 대입 연산에 따른 각 객체간의 데이터 흐름
![각 노드들의 대입 연산에 따른 각 객체간의 테이터 흐름](/assets/img/projects/chat_system/Node Flow Chart.png) 


(2) CRoom : 접속자들이 실제로 상주하며 활동할 수 있도록 이들을 묶어두는 객체로 대화방 그 자체라고도 할 수 있다.

a) static int m_sNum : 초기 CRoom 객체 초기화시 방 번호 할당에 쓰이는 정적 변수

b) m_nNum : 대화방 번호

c) CGuestNode *m_pHost : 호스트 권한을 보유한 접속자에 대한 포인터 변수

d) DLinkedList<CGuestNode> m_GuestList : 대화방 접속자들의 목록


(3) CRoomFlag : 대화방에 필요한 각종 정보들을 담고 있는 객체

a) BitVector m_Occupy : 대응되는 각 CRoom 객체의 대화방 입장 가능 유무를 다루는 객체

b) BitVector m_Locks : 대화방의 생성, 삭제 과정 중에 유저들이 대화방에 난입하는 것을 막기 위해 CRoom의 입장 처리에 대한 동기화에 쓰이는 객체

c) TCHAR m_Title[MaxRoom][TitleMaxLen] : 대화방의 이름

d) TCHAR m_Pass[MaxRoom][PassMaxLen] : 대화방의 입장 비밀번호

e) TCHAR m_HostName[MaxRoom][MaxLen] : 대화방 방장의 닉네임

f) bool m_bIsPrivate[MaxRoom] : 대화방의 속성, 즉 공개, 비공개의 유무를 다루는 일종의 플래그 변수

(4) CConnectors : 채팅 서버 전체 접속자 목록을 유저코드를 사용하여 이진 트리 형태로 유지하고 다루는 객체

a)  BinaryTree<CConnector> m_cConnectorList : 채팅 서버 전체 접속자 목록으로 리스트가 아닌 이진 트리 형태로 되어 있다.
이는 리스트 보다 검색 속도에서 유리한 자료 구조 중 이진 트리가 가장 구현하기 편하기 때문이라는 단순한 이유로 사용된 것이다.


(5) CConnector : 서버에 접속중인 클라이언트들의 목록을 다루는 CConnectors 객체 내부에서 클라이언트에 대한 정보를 담기 위해 사용 된다.

이는 대체로 특정 클라이언트가 로그인시 입력한 ID의 계정이 현재 서버에 접속 중인지 아닌지 판별하는 용도로 주로 사용 된다.

그리고 CDBNode 타입 객체에서 CConnector 타입 객체 방향으로의 단방향 대입 연산을 통해 CDBNode 타입 객체가 가지고 있는 데이터를 읽어올 수 있다.

a) SOCKET m_hClntSock : 접속한 클라이언트의 소켓에 대한 핸들

b) SOCKADDR_IN m_clntAdr : 접속한 클라이언트의 IP 주소

c) TCHAR m_ID[MaxLen] : 접속한 클라이언트 계정의 ID

d) int m_nUserCode : 클라이언트의 계정을 구분하는 고유 번호로써 서버의 자료구조 내에서 조금더 빠른 조회 등을 위해 계정의 ID 대신 사용된다.


(6) CDBNode : DB의 데이터를 읽어 오거나, DB에 데이터를 기록 할때 사용하는 일종의 버퍼 객체로 CConnector 타입 객체와 CClntNode 타입 객체와 데이터를 주고 받을 수 있게끔 이들 하고만 대입 연산이 가능하다.

a) TCHAR m_ID[MaxLen] : 접속한 클라이언트 계정의 ID

b) TCHAR m_Pass[MaxLen] : 접속한 클라이언트 계정의 Password

c) TCHAR m_NickName[MaxLen] : 접속한 클라이언트 계정의 대화명

d) int m_nUserCode : 클라이언트의 계정을 구분하는 고유 번호로써 서버의 자료구조 내에서 조금더 빠른 조회 등을 위해 계정의 ID 대신 사용된다.

e) bool m_bDirty : 데이터가 수정 되었는지를 판별하는 플래그 변수로 만약 이 값이 true인 경우 이 객체 내의 데이터는 해당 클라이언트가 Log out 할때 DB에 저장하는 작업이 수행 된다.

f) bool m_bIsBanned : 현재 이 객체에 정보가 담긴 클라이언트가 Login 제한에 걸려 있는지의 유무로 이 값이 true 인 경우 제한이 걸려있는 계정으로 판별한다.


(7) CClntNode : 접속한 클라이언트의 원본 데이터를 담고 있는 객체로써 실질적으로 IOCP 상에서 움직이고 다루어지는 핵심 객체이다.
CDBNode 타입 객체와 CClntInfo 타입 객체와 데이터를 주고 받을 수 있게끔 이들 하고만 대입 연산이 가능하다.

이 객체의 정보가 각기 다른 자료구조들로 복사 되어 해당 루틴에서 그 복사본을 토대로 명령어를 수행하고, 복사본이 변경된 경우 이 변경된 값이 최종 반영되는 객체.

a) SOCKET m_hClntSock : 접속한 클라이언트의 소켓에 대한 핸들

b) SOCKADDR_IN m_clntAdr : 접속한 클라이언트의 IP 주소

c) TCHAR m_ID[MaxLen] : 접속한 클라이언트 계정의 ID

d) TCHAR m_Pass[MaxLen] : 접속한 클라이언트 계정의 Password

e) TCHAR m_NickName[MaxLen] : 접속한 클라이언트 계정의 대화명

f) int m_nUserCode : 클라이언트의 계정을 구분하는 고유 번호로써 서버의 자료구조 내에서 조금더 빠른 조회 등을 위해 계정의 ID 대신 사용된다.

g) bool m_bDirty : 데이터가 수정 되었는지를 판별하는 플래그 변수로 만약 이 값이 true인 경우 이 객체 내의 데이터는 해당 클라이언트가 Log out 할때 DB에 저장하는 작업이 수행 된다.

h) EClntType m_ClntType : 현재 CClntNode 객체가 다루고 있는 클라이언트의 종류로는 USER = 일반 채팅 사용자, LOGSERVER = 로그를 기록하는 로그 서버가 있다.

i) int m_nRoomIndex : 현재 클라이언트가 위치한 대화방의 번호로 NotInRoom(-1), Lobby(0), Room(1~MAX)의 값을 가진다.

j) m_bIsOnConnectorList : 현재 서버 접속자 리스트에 등록 되어 있는지, 즉 현재 서버에 접속 중인지 아닌지를 판별할때 쓰이는 변수로, 클라이언트의 연결 종료시 해당 클라이언트가 아직 Login 되지는 않았지만 서버와는 연결된 상태일때 이를 구별하여 안정적으로 종료키 위해, 자료구조상의 데이터들을 처리하기 위해 사용 된다.


(8) CClntInfo : CClntNode와 CGuestNode 사이에서 버퍼 역할을 하는 객체로 CClntNode타입 객체와 CGuestNode 타입 객체와 데이터를 주고 받을 수 있게끔 이들 하고만 대입 연산이 가능하다.

CClntNode와 CGuestNode가 직접 데이터를 주고 받는 경우 mutex 처리로 인해 발생 할 수 있는 데드락 현상의 예방 및 양쪽 객체의 동기화 처리로 인해 다른 객체의 처리 시간이 지연되는 문제를 줄이기 위한 목적으로도 사용된다.

a) SOCKET m_hClntSock : 접속한 클라이언트의 소켓에 대한 핸들

b) SOCKADDR_IN m_clntAdr : 접속한 클라이언트의 IP 주소

c) TCHAR m_ID[MaxLen] : 접속한 클라이언트 계정의 ID

d) TCHAR m_Pass[MaxLen] : 접속한 클라이언트 계정의 Password

e) TCHAR m_NickName[MaxLen] : 접속한 클라이언트 계정의 대화명

f) int m_nUserCode : 클라이언트의 계정을 구분하는 고유 번호로써 서버의 자료구조 내에서 조금더 빠른 조회 등을 위해 계정의 ID 대신 사용된다.

g) bool m_bDirty : 데이터가 수정 되었는지를 판별하는 플래그 변수로 만약 이 값이 true인 경우 이 객체 내의 데이터는 해당 클라이언트가 Log out 할때 DB에 저장하는 작업이 수행 된다.

h) int m_nRoomIndex : 현재 클라이언트가 위치한 대화방의 번호로 NotInRoom(-1), Lobby(0), Room(1~MAX)의 값을 가진다.

i)  CClntNode *m_pClntNode : ClntNode에 대한 포인터


(9) CGuestNode : 대화방에 접속한 클라이언트의 정보를 담는 객체로 CRoom 객체 내부의 리스트 객체의 자료형으로 사용된다.
CClntInfo 타입의 객체와 데이터를 주고 받을 수 있게끔 이 타입의 객체와만 대입 연산이 가능하다.

a) SOCKET m_hClntSock : 접속한 클라이언트의 소켓에 대한 핸들

b) SOCKADDR_IN m_clntAdr : 접속한 클라이언트의 IP 주소

c) TCHAR m_ID[MaxLen] : 접속한 클라이언트 계정의 ID

d) TCHAR m_Pass[MaxLen] : 접속한 클라이언트 계정의 Password

e) TCHAR m_NickName[MaxLen] : 접속한 클라이언트 계정의 대화명

f) int m_nUserCode : 클라이언트의 계정을 구분하는 고유 번호로써 서버의 자료구조 내에서 조금더 빠른 조회 등을 위해 계정의 ID 대신 사용된다.

g) bool m_bDirty : 데이터가 수정 되었는지를 판별하는 플래그 변수로 만약 이 값이 true인 경우 이 객체 내의 데이터는 해당 클라이언트가 Log out 할때 DB에 저장하는 작업이 수행 된다.

h) bool m_bIsHost : 현재 CGuestNode 객체에 담겨 있는 클라이언트가 해당 방의 방장인지 아닌지의 유무로 이 값이 true인 경우 방장 권한을 가지고 있는 것으로 판별한다.

i)  CClntNode *m_pClntNode : ClntNode에 대한 포인터


(10) CDB : DBMS로 부터 SQL 질의문들을 통해 그 결과 값들을 읽어 오거나 저장하는 객체

a) TCHAR *m_pDBName : 데이터 베이스 이름(파일모드시 : 파일 이름, ODBC 이용시 : 데이터 소스 이름)

b) EDBTYPE m_eDBType : 취급할 DBMS의 타입 (File : 파일시스템, DBMS : MS-SQL)

c) BinaryTree<IPNode> IPList : 서버 연결 자체가 금지된 IP주소의 리스트로 DB에서 한 번에 리스트 전체를 메모리로 로드하여 보관한다.

d) SQLHENV hEnv : ODBC를 위한 환경 핸들

e) SQLHDBC hDbc : ODBC를 위한 연결 핸들

f) SQLHSTMT hStmt : ODBC를 위한 질의문에 대한 핸들

g) TCHAR Column[MaxLenOfIP] : ODBC를 통해 DBMS로 부터 결과값을 읽어올 변수


4\) 명령어 처리 함수

(1) bool NormalMessage(CParam *p_pArg) : 일반 메시지 처리

클라이언트로 부터 명령 코드가 0인 메시지를 받아, 현재 클라이언트가 위치한 장소의 접속자들에게 메시지를 전파 한다.


(2) bool JoinRoom(CParam *p_pArg) : Room에 대한 입장 요청 처리1

클라이언트로 부터 명령 코드가 1인 메시지를 받아 함께 수신된 옵션 필드의 대화방 번호와 대응되는 대화방에 대한 입장 작업을 수행 한다.


(3) bool JoinRoomWithTitle(CParam *p_pArg) : Room에 대한 입장 요청 처리2

클라이언트로 부터 명령 코드가 2인 메시지를 받아 함께 수신된 옵션 필드의 대화방 제목과 일치하는 대화방에 대한 입장 작업을 수행 한다.


(4) bool LeaveRoom(CParam *p_pArg) : 퇴장 처리

클라이언트로 부터 명령 코드가 3인 메세지를 받아 현재 클라이언트가 위치한 CRoom 객체에서 클라이언트의 정보를 가진 노드를 제거하여 퇴장 처리를 한다.


(5) bool CreateRoom(CParam *p_pArg) : 대화방 생성 처리

클라이언트로 부터 받은 정보를 사용하여 대화방을 생성 한다.
이 과정에서 서버내 모든 대화방들의 정보(ex : 대화방 이름, 번호, 입장 비밀번호 등)를 가지고 있는 CRoomsFlag 객체들을 조회하여 대화방 이름 중복 문제 등을 판별하고 대화방의 생성 유무를 결정, 그에 맞는 결과(생성 가능일때 대화방 생성)를 돌려준다.


(6) bool DestroyRoom(CParam *p_pArg) : 대화방 삭제 처리

대화방 삭제를 요청한 클라이언트가 머물고 있는 대화방을 삭제하기 위한 일련의 작업들을 수행한다.
삭제가 아니라 삭제와 관련된 작업들을 수행하는 것이기 때문에, 실제로 이 함수 내부에서는 CRoom[] 객체와 CRoomsFlag 객체를 초기화 하는 작업을 수행하지는 않으며 부수적으로 해당 대화방과 대응되는 CRoomsFlag 객체를 Lock시켜 삭제가 진행중인 대화방에 다른 클라이언트들이 난입 하는 것을 미연에 방지하는 중요한 역할을 수행한다.
이 DestroyRoom 내에서 설정된 Lock은 LeaveRoom 함수에서 CRoom[]과 CRoomsFlag 객체가 초기화 될때 호출 되는 Clear 멤버 함수에 의해 해제 되게 된다.
다시 말해 이는 대화방이 서버내 자료구조 상에서 완전히 삭제 처리 되는 작업은 DestroyRoom 함수가 아니라 LeaveRoom 함수 내에서 수행된다는 것을 의미한다.
즉 DestroyRoom 함수는 대화방에 접속해 있는 모든 클라이언트들로 하여금 서버측에 LeaveRoom 작업을 요청 하도록 명령하는 메시지를 송신할 뿐이지 직접적으로 대화방을 삭제 하는 작업을 수행 하지는 않는다.


(7) bool KickOut(CParam *p_pArg) : 대상 추방 처리

실제로 이 함수 내부에서는 대상을 추방하는 일을 수행 하지 않는다.
수신된 대화명과 동일한 대화명을 가진 유저를 대화방에서 찾고 대상이 존재하면 대상이 되는 클라이언트에게 서버에 LeaveRoom 작업을 요청 하도록 하는 RequestLeave 메시지를 전달하는 일을 한다.


(8) bool ChangeRoomTitle(CParam *p_pArg) : 대화방명 변경 처리

현재 클라이언트가 위치해 있는 대화방의 이름을 변경한다.

CRoomsFlag 객체 내부에서 현재 위치한 대화방과 대응되는 인덱스의 대화방명을 찾아 중복 검사 및 변경 작업들을 수행 한다.


(9) bool ChangeRoomPass(CParam *p_pArg) : 대화방 비밀번호 변경 처리

현재 클라이언트가 위치해 있는 대화방의 입장 비밀번호를 변경한다.

CRoomsFlag 객체 내부에서 현재 위치한 대화방과 대응되는 인덱스의 비밀번호를 찾아 중복 검사 및 변경 작업들을 수행 한다.


(10) bool LoginCheck(CParam *p_pArg) : 로그인 처리

클라이언트로 부터 수신한 계정 정보(ID, Password)를 DBMS에 조회를 요청하여 DB내에 일치하는 데이터가 존재하면 "SuccessfulLogin" 메시지를 클라이언트에 보내 접속 허가를, 그렇지 않으면 수신된 계정 정보를 토대로 계정을 DBMS를 통해 DB에 등록하여 새 계정을 생성한 다음, 클라이언트가 대화명을 생성할 수 있도록 클라이언트에게 대화명 생성 모드로의 이행을 지시하는 메시지를 보낸다.

부수적으로는 로그인 요청을 해온 계정이 접속 제한이 되어 있는 계정인지 DBMS를 통해 조회하고 그 결과에 따라 접속을 허가 또는 거부한다.


(11) bool ChangeNickName(CParam *p_pArg) : 대화명 변경 처리

GuestNode의 대화명을 클라이언트로 부터 수신한 대화명으로 변경한다.
이때 대화명이 변경 되었다는 Dirty 플래그가 set 되며 이렇게 변경된 대화명은 Log out시점에서 DBMS를 통해 DB에 반영되게 된다.

이 함수는 변경된 대화명과 이에 대한 알림을 대화방 내의 모든 클라이언트들에게 보내는 역할도 수행한다.

대화명은 다른 유저들의 대화명과 중복 사용이 가능하다.


(12) bool GiveHostship(CParam *p_pArg) : 방장 권한 부여 처리

요청한 클라이언트가 가지고 있는 방장 권한을 지정된 클라이언트로 변경한다.

방장 권한은 CRoom[] 객체 내의 멤버 변수 *m_pHost가 방장의 Guest 노드를 가리키고 있는 형태로 유지되고 있으며 방장 권한 부여(변경)시에는 이 포인터의 대상을 변경하는 방식으로 작동한다.


(13) bool CreateNickName(CParam *p_pArg) : 대화명 생성 요청 처리

클라이언트로 부터 수신한 대화명을 클라이언트의 ClntNode에 반영하여 새 대화명으로 설정한다.


(14) bool UpdateRoomList(CParam *p_pArg) : 대화방 목록 갱신 처리

CRoomsFlag 객체 내부를 순회하며 방번호, 이름 등의 대화방 정보들을 클라이언트에게 전송한다.


(15) bool UpdateGuestList(CParam *p_pArg) : 대화방 접속자 목록 갱신 처리

클라이언트가 위치한 대화방가 대응되는 cRoom[] 객체의 접속자 목록을 클라이언트에게 전송 한다.


(16) bool ShutdownServer(CParam *p_pArg) : 서버 강제 종료 처리

Lobby 부터 시작하여 모든 대화방을 순차적으로 순회하며 각 대화방에 접속해 있는 클라이언트들과의 연결을 종료한다.


(17) bool Ban(CParam *p_pArg) : 특정 계정의 접속 차단 처리

수신된 ID의 계정을 로그인을 하지 못 하도록 제한 한다.
DBMS를 통해 DB내에 해당 ID가 존재하는지 확인 후 존재한다면 대상의 ID를 DB내 Ban 목록에 등록 한다.

이후 대상 클라이언트에게 서버에 EOF 메시지를 전송하도록 지시하는 RequestEOFMessage 메시지를 보낸다.


(18) bool UnBan(CParam *p_pArg) : 특정 계정의 접속 차단 해제 처리

수신된 ID의 계정의 로그인 제한을 해제한다.
해당 ID의 계정이 DB에 존재하는 경우 이와 일치하는 ID의 계정을 Ban 테이블에서 찾아 삭제 한다.


(19) bool ForcedToDisconnect(CParam *p_pArg) : 특정 사용자 원격 종료 처리

현재 접속해 있는 사용자(클라이언트)를 강제로 서버와 연결 종료 시킨다.

클라이언트로 부터 수신 받은 ID와 일치하는 정보를 가진 클라이언트가 CConnectorList 객체 내의 접속자 목록에 존재하는 경우 대상에게 RequestEOFMessage 메시지를 보내 서버로 EOF 메시지를 발신하도록 지시한다.


(20) bool IPBan(CParam *p_pArg) : 특정 IP의 서버 접속 차단 처리

해당 IP의 서버 접속을 제한한다.

지정된 IP 사용자의 서버 접속을 제한 하기 위해, 해당 IP를 DB내 Ban IP 테이블에 등록한다.

이후 CConnectorList 객체 내의 접속자 목록에 해당 IP를 가진 클라이언트가 존재하는 경우,  이 클라이언트에게 RequestEOFMessage 메시지를 전송하여 서버로 EOF 메시지를 발신토록 한다.


(21) bool UnIPBan(CParam *p_pArg) : 특정 IP의 서버 접속 차단 해제 처리

해당 IP의 서버 접속 제한을 해제한다.

해당 IP가 DB내 Ban IP 테이블에 존재하는지 확인하고, 존재한다면 이를 삭제한다.


(22) bool NotifyMessage(CParam *p_pArg) : 전체 알림말 전송 처리

cRoomsFlag 객체의 대화방 유효 플래그를 지표 삼아, 전체 대화방을 순회하며 개설되어 있는 각 대화방의 모든 사용자들에게 순차적으로 메시지를 전송 한다.


(23) bool CountPeople(CParam *p_pArg) : 서버 전체 접속자수 조회 처리

CConnectorList 내의 노드들을 순회하며 현재 접속한 유저들의 수를 계산하고 이에 대한 결과를 클라이언트에게 돌려 준다.


(24) void WriteLog(TCHAR *p_pID, sockaddr_in *p_pClntAdr ,TCHAR *p_pMsg) : 로그 서버로 메시지 전송

클라이언트의 계정 ID와 IP주소, 기록할 메시지 내용을 인자값으로 받아 "[년도-월-일 시:분:초] [ID:IP] : 내용"과 같은 양식의 메시지를 조합하여 로그 서버로 전송 한다.
만약 로그 서버와 연결되어 있지 않으면 데이터를 전송하지 않고 그대로 반환 한다.


#### Client

1\) 클라이언트의 상태 전이
  
![클라이언트들의 상태 전이](/assets/img/projects/chat_system/client_state_transition.png) 


Client는 내부적으로 서버로 부터 수신 받은 메시지를 이벤트로 삼아 계정 정보 입력, 대화명 생성, 서버내에서의 채팅이라는 3가지의 상태로 전이하는 형태로 작동 한다.

예를 들어, 계정 ID와 Password를 입력하여 서버에 인증 요청을 하고, 서버로 부터 인증 성공의 메세지를 받으면 로그인 모드에서 채팅 모드 상태로 전이된다(이때 클라이언트의 서버 내에서의 위치는 Lobby가 된다).

(1) 로그인 모드 : 채팅 서버의 로비로 입장하기 위해 사용자 계정 인증 관련 메시지를 주고 받을 수 있는 상태

(2) 대화명 생성 모드 : 채팅 서버에 접속하여 계정의 대화명을 새로 지정할 수 있는 상태

(3) 채팅 모드 : 본격적으로 다른 클라이언트들과 메시지를 주고 받으며 대화를 나눌 수 있는 상태


2\) 구현 클래스 다이어그램
![구현 클래스 다이어그](/assets/img/projects/chat_system/class_uml.png) 


3\) 자료구조

(1) CMyClient : 클라이언트의 상태 전이를 총괄하는 객체

a) AStack<CClntState*> m_aClntState : 클라이언트의 상태 객체에 대한 포인터를 데이터 타입으로 하는 스택 구조의 객체다.

클라이언트는 로그인, 닉네임 생성, 채팅 3가지 모드에 맞춰 실행되는데 이는 각각의 상태에 대응되는 객체들의 실행으로 이루어진다.
이때 이 대응되는 각각의 객체들을 가리키기 위해 CClntState* 타입의 포인터 변수를 사용하는데, 이들을 데이터 타입으로 배열 구조 형식의 스택으로 만들어 상태 전이를 보다 용이하게 하기 위한 목적으로 사용한다.


(2) CRoomInfo : 대화방의 정보를 보관하는 객체

a) TCHAR m_Title[TitleMaxLen] : 대화방 제목

b) TCHAR m_HostName[NickMaxLen] : 대화방 방장(호스트)의 대화명

c) bool m_bPrivate : 대화방의 속성(false : 공개방, true : 비공개방)

d) int m_nNum : 대화방 번호

본 객체는 다음과 같이 클라이언트가 현재 위치한 대화방의 정보와 서버로 부터 전송 받은 서버내 모든 대화방들의 정보를 목록화 하여 보관 하는 용도로 사용되기도 한다.

DLinkedList<CRoomInfo> cRoomList


(3) CGuestInfo : 현재 클라이언트가 위치한 대화방의 각 접속자들의 정보를 보관하는 객체

a) int m_nUserCode : 유저코드

b) TCHAR m_NickName[NickMaxLen] : 대화명

c) bool m_bIsHost : 방장 권한 보유 유무(false : 방장이 아님, true : 방장)

본 객체는 다음과 같이 클라이언트가 현재 위치한 대화방내 모든 접속자들의 정보를 서버로 부터 전송 받아 목록화 하여 보관하는 용도로 사용된다.

DLinkedList<CGuestInfo> cGuestList


4\) Client 내에만 존재하는 보조 함수들

(1) int FindPass(TCHAR *p_pInst) : p_pInst가 가리키고 있는 메시지 내에서 Password 필드를 찾아 해당 필드가 시작되는 위치의 offset을 리턴 한다.

a) 반환 값 : -1 : 패스워드가 존재하지 않는다
                그 외의 값 : 패스워드 필드의 offset
b) p_pInst : 사용자가 입력한 메시지


(2) int FindTitle(TCHAR *p_pInst) : p_pInst가 가리키고 있는 메시지 내에서 Title 필드를 찾아 해당 필드가 시작되는 위치의 offset을 리턴 한다,

a) 반환 값 : -1 : Title이 존재하지 않는다
                그 외의 값 : Title 필드의 offset
b) p_pInst : 사용자가 입력한 메시지


(3) void ReformJoinRoomMsg(int p_nOffsetForBody, TCHAR *p_pInst) : 사용자가 입력한 "/join" 명령 메시지의 전문을 서버로 전송하기 전에, 명령어 필드를 제외한 다른 필드 모두를 사용자 정의 프로토콜에 맞게 변환 한다.

 ex : 호출 전의 p_pInst의 메시지 : "/join Hello #1234"
      호출 후의 p_pInst의 메시지 : "/join 5 Hello 4 1234"


a) 반환 값: 없음, p_pInst가 가리키는 메시지의 본문이 변경 된다.

b) p_nOffsetForBody : 명령어 "/Join" 필드를 건너 뛴, 메시지 본문이 시작되는 필드의 offset

c) p_pInst : 사용자가 입력한 메시지


(4) void ReformCreateRoomMsg(int p_nOffsetForBody, TCHAR *p_pInst) : 사용자가 입력한 "/cr" 명령 메시지의 전문을 서버로 전송하기 전에, 명령어 필드를 제외한 다른 필드 모두를 사용자 정의 프로토콜에 맞게 변환 한다.

 ex : 호출 전의 p_pInst의 메시지 : "/cr Hello #1234"
      호출 후의 p_pInst의 메시지 : "/cr 5 Hello 4 1234"

a) 반환 값: 없음, p_pInst가 가리키는 메시지의 본문이 변경 된다.

b) p_nOffsetForBody : 명령어 "/cr" 필드를 건너 뛴, 메시지 본문이 시작되는 필드의 offset

c) p_pInst : 사용자가 입력한 메시지


(5) void ReformDestroyRoomMsg(int p_nOffsetForBody, TCHAR *p_pInst) : 사용자가 입력한 "/dr" 명령 메시지의 전문을 서버로 전송하기 전에, 명령어 필드를 제외한 다른 필드 모두를 사용자 정의 프로토콜에 맞게 변환 한다.

 ex : 호출 전의 p_pInst의 메시지 : "/dr 1234"
      호출 후의 p_pInst의 메시지 : "/dr 4 1234"


a) 반환 값: 없음, p_pInst가 가리키는 메시지의 본문이 변경 된다.

b) p_nOffsetForBody : 명령어 "/dr" 필드를 건너 뛴, 메시지 본문이 시작되는 필드의 offset

c) p_pInst : 사용자가 입력한 메시지



5\) 수신 메시지 처리 함수

(1) void Execute(SOCKET p_hSock, int p_nOPNum, TCHAR *p_pMsg) : 서버로 부터 메시지를 전송 받아 명령 코드에 맞추어 그에 대응되는 메소드들을 호출한다.


(2) void OutputMsg(TCHAR *p_pMsg) : 서버로 부터 받은 메시지를 출력 한다.


(3) void ShowHelp() : 명령어 "/h" 입력시 도움말을 출력 한다.


(4) void OutputRoomList() : 서버내 모든 대화방들의 정보를 목록화 하여 출력 한다.

CRoomList 객체에 보관 중인 서버로 부터 전송 받은 대화방들의 정보를 첫 노드 부터 순차적으로 출력 한다.


(5) void OutputGuestList() : 대화방내 모든 접속자들의 정보를 목록화 하여 출력 한다.

CGuestList 객체에 보관 중인 서버로 부터 전송 받은 대화방내 접속자들의 정보를 첫 노드 부터 순차적으로 출력 한다.


(6) void AppendRoomToList(TCHAR *p_pMsg) : 서버로 부터 하나의 대화방 정보를 전송 받아 CRoomList 객체에 추가한다.


(7) void AppendGuestToList(TCHAR *p_pMsg) : 서버로 부터 한 명의 대화방 접속자의 정보를 전송 받아 CGuestList 객체에 추가한다.


(8) void RemoveGuestFromList(TCHAR *p_pMsg) : 서버로 부터 유저 코드를 전송 받아 CGuestList에서 유저 코드가 일치하는 접속자 정보 노드를 제거 한다.


(9) void ModifyGuestNicknameOnList(TCHAR *p_pMsg) : 서버로 부터 유저 코드와 닉네임을 전송 받아 CGuestList에서 유저 코드가 일치하는 접속자 정보 노드의 닉네임을 전송 받은 닉네임으로 변경 한다.


(10) void ModifyHostOnList(TCHAR *p_pMsg) : 서버로 부터 유저 코드를 전송 받아 CGuestList 객체 내에서 유저 코드가 일치하는 접속자를 새로운 대화방의 방장으로 변경한다.

기존의 방장은 그 권한 플래그를 해제하고 CCurrentRoom 객체의 방장명 항목도 새로 변경된 방장의 대화명으로 변경한다.


(11) void StoreRoomInformation(TCHAR *p_pMsg) : CCurrentRoom 객체의 데이터(방번호, 대화방 이름 길이, 대화방 이름, 방장의 대화명 길이, 방장의 대화명, 대화방의 속성)를 서버로 부터 전송 받은 데이터들로 갱신 한다.


(12) void ChangeNickName(TCHAR *p_pMsg) : 서버로 부터 전송 받은 대화명을 현재 사용자의 클라이언트가 채팅시 사용하는 대화명으로 설정 한다.


#### Log Server

Log Server는 Main Server로 부터 클라이언트들의 명령 수행에 관한 로그를 받아 텍스트 파일에 기록하는 작업을 수행 한다.

메시지를 수신하여 이를 분해 하는 과정은 Main Server, Client와 같지만 명령 코드를 추출하는 단계가 존재치 않고, 명령 코드와 대응 되는 처리 함수를 호출하는 대신 수신한 메시지를 텍스트에 기록하는 함수를 호출한다.

![log server screenshot 1](/assets/img/projects/chat_system/logserver_outline0.png) 

이처럼 간단한 기능만을 수행 하도록 만들어졌기 때문에 Main Server로 부터 IOCP 방식이 아닌 일반적인 동기 모드의 recv 함수 호출을 통해 데이터를 단방향으로 수신 한다.

다음은 메시지를 로그 파일 기록 함수의 작동 과정을 개략적으로 표현한 순서도다.

![log server screenshot 2](/assets/img/projects/chat_system/logserv_outline2.png) 

실제 코드상에서 로그 파일의 날짜를 다루는 방법은 순서도에서 개략적으로 표현한 것과는 조금 다르다.

로그 서버로 들어오는 메시지에는 Main Server의 시간이 함께 기록되어 있는데 이를 로그 서버 프로그램이 처음 실행될 때 기록해둔 로그 서버 시간과 비교하는 것이다.

그리고 비교 결과 서로 시간이 다르면, Main Server에서 받은 시간으로 Log Server의 시간 변수 값을 변경한 다음 그에 맞춰 다른 로그 파일을 불러오고 기존 로그 파일을 닫는다.

여기서 로그 파일의 이름은 날짜 값을 그대로 사용하며 "fopen("날짜.log", "at")"과 같은 함수 호출 방식으로 텍스트 파일로 추가 작성 및 파일이 존재치 않으면 새로 작성하는 모드를 사용한다.


#### DB Server

원격 또는 로컬에 위치한 DB Server와 통신하는 DBMS 모드와
Main Server가 동작하는 로컬 상에서 파일 시스템을 통해 동작하는 File System 모드 모두 지원한다.

기본적으로는 DBMS 모드가 default 설정되어 동작하며, 이때의 DB Server는 로컬/원격지 그 어느 곳에 있어도 문제 되지 않는다.

만약, Main Server 구동 시,  ODBC 설정 문제 또는 네트워크 문제 등으로 DB Server와 연결이 불가능한 경우에는 Main Server가 동작하는 로컬 디스크 경로에 text 형식의 파일을 생성하여 DB 파일을 관리하는 File System 모드로 변경되어 동작한다.


1\) DBMS 모드

MS-SQL Server 2012를 사용하여 Windows에서 제공하는 ODBC를 통해 Main Server와 연동된다.

다양한 DBMS들과의 유연한 연동을 지원하기 위해 Windows의 ODBC 방식을 채택한 것이기 때문에 Windows의 ODBC 설정만 정상적으로 되어 있다면, 굳이 MS-SQL Server 2012 뿐만 아니라 Oracle과 같은 다른 DBMS들과도 연동이 가능하다.


![db server screenshot 1](/assets/img/projects/chat_system/db_startup_flow.gif) 

Main Server가 구동을 시작 할 때 DBMS에 접속을 성공한 경우, 데이터베이스와 테이블이 DBMS에 존재하지 않는 경우 이들을 새롭게 생성한다.

그리고 서버에서 구동 중인 상황에서 차단 IP 추가/삭제, 사용자 NickName과 같은 변경 내역들은 Writeback 메커니즘과 유사하게 서버 종료 시 DBMS에 반영된다.

차단 IP 등록/해제 시에는, 서버 내부에 차단된 IP 값들을  보관하고 있다가 서버 종료 시, DBMS 에 반영한다.

클라이언트가 NickName을 처음 생성 하였을 때에는 이를 DBMS에 바로 저장하지 않고
클라이언트 종료 시에 DBMS에 반영한다.

한편, 사용자 계정 접속 차단 등록/해제 시에는, 그 변경 내역이 바로 DBMS에 반영된다.

다음은 내부 스키마 정의 내역이다.

- Database 이름 : AccountDB

<table>
  <thead>
    <tr>
      <th>Table</th>
      <th>Column</th>
      <th>Type</th>
      <th>Null 여부</th>
      <th>Key</th>
      <th>Default</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5">Account</td>
      <td>ID</td>
      <td>NVARCHAR(13)</td>
      <td>X</td>
      <td>PK</td>
      <td></td>
      <td>사용자 ID</td>
    </tr>
    <tr>
      <td>Pass</td>
      <td>NVARCHAR(13)</td>
      <td>X</td>
      <td></td>
      <td></td>
      <td>비밀번호</td>
    </tr>
    <tr>
      <td>NickName</td>
      <td>NVARCHAR(13)</td>
      <td>O</td>
      <td>NOT UK</td>
      <td></td>
      <td>대화명</td>
    </tr>
    <tr>
      <td>UserCode</td>
      <td>INT</td>
      <td>X</td>
      <td></td>
      <td>0 부터 1씩 증가</td>
      <td>사용자 번호</td>
    </tr>
    <tr>
      <td>IsBanned</td>
      <td>BIT</td>
      <td>O</td>
      <td></td>
      <td></td>
      <td>차단 여부</td>
    </tr>
    <tr>
      <td>BannedIP</td>
      <td>IP</td>
      <td>NVARCHAR(13)</td>
      <td>X</td>
      <td>PK</td>
      <td></td>
      <td>차단 IP</td>
    </tr>
  </tbody>
</table>

2\) File System 모드

Main Server가 동작하는 로컬 디스크 경로 상에 Account.dat 이라는 이름의 텍스트 파일을 생성하여 DB 정보를 관리한다.

File System 모드에서는 IP 차단과 사용자 계정 차단 시 그 차단 정보를 DB 파일에 저장하지 않기 때문에, 서버 재구동 시 차단 목록이 모두 초기화 된다.