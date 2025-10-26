---
title: 바다야 내가 간다
description: GNEX 환경에서 개발한 팀 프로젝트 게임
author: hyeonsu-choe
date: 2008-01-28 00:39:00 +0900
last_modified_at: 2025-10-27 21:37 +0900
categories: [Projects, GNEX]
tags: [team, mobile, game, gnex, c]
toc: true
pin: false
render_with_liquid: false
image:
  path: /assets/img/projects/going_to_the_sea/sw2.JPG
  # lqip: 
  alt: Gameplay screenshot of 바다야 내가 간다
---

| 구분 | 내용 |
|:----|:----|
|플랫폼|GNEX|
|개발 도구 및 사용 언어|GNEX SDK, Mobile C|
|개발 기간| 2004년 11월 ~ 2004년 12월|
|개발 단위| 팀 프로젝트 |
|역할|프로그래밍 전담|


## 1. 프로그램 소개

2000년도 초반 유행하던 장르인 타이밍에 맞춰 버튼 누르기 게임으로 화면 하단의 타이밍 게이지가 지나갈 때 그에 맞추어 해당 하는 버튼을 눌러 화면 중앙의 플레이어를 목표 지점 까지 무사히 데리고 가는 것이 목표인 게임이다.

학교에서 주관하는 프로젝트 발표회를 위해 팀 프로젝트 차원에서 제작한 게임으로 본인은 프로그래밍 파트를 전담 하였다.

본래 기획 초안에는 스토리 모드를 통해 게임을 익히고 도전 모드를 통해 다른 유저들과 포인트를 놓고 랭킹을 경쟁 할 수 있게 되어 있었지만 사정상 이 부분을 난이도 높은 싱글 모드로 변경 하여 제작 하였다.

버튼을 일정 횟수 이상 잘못 누르면 유저의 HP 포인트가 깍여 나가게 되고 HP가 0이 되면 게임 오버가 된다.

또한, 게임 중간 중간 아이템들이 나오는데 이는 해당 버튼을 실수 없이 적재 적소에 눌러 퍼펙트를 띄웠을때 획득 가능하다.

아이템의 종류로는 HP를 회복 시켜 주거나 버튼 입력 순서가 반전된 상태를 회복 시켜 주는 것들이 있다.

### 로고 & 타이틀 화면

---

로고 화면, 팀 이름을 보여 준다.

![팀 로고 화면](/assets/img/projects/going_to_the_sea/sw1.JPG)  
![타이틀 화면](/assets/img/projects/going_to_the_sea/sw2.JPG)  


### 메뉴 화면

---

![메뉴 화면](/assets/img/projects/going_to_the_sea/sw_menu.JPG) 

* 스토리 모드 : 스토리에 따라 게임을 진행, 일종의 연습용 싱글 플레이 모드로 난이도가 낮다.

* 이벤트 모드 : 도전하고 싶은 스테이지를 임의로 선택하여 진행하는 일종의 도전 모드  
타임 게이지의 진행 속도, 키 입력 순서 반전 발생의 빈도, 버튼 입력 실패시 깍여 나가는 체력 포인트의 크기 등이 스토리 모드 보다 높게 설정 되어 있어 난이도가 높다. 

본래라면 포인트를 쌓아 다른 유저들과 랭킹 경쟁을 할 수 있는 모드였지만 이 부분이 삭제 되면서 난이도가 좀더 높은 싱글 플레이 모드로 변경 되었다.


### 조작 방법 설명 화면

---

아무 버튼을 누르면 현재 눌린 버튼을 표시해 준다.

![조작 방법 설명 화면](/assets/img/projects/going_to_the_sea/sw3.JPG) 


### 플레이어 선택 화면

---

![플레이어 선택 화면1](/assets/img/projects/going_to_the_sea/sw4.JPG)  
![플레이어 선택 화면2](/assets/img/projects/going_to_the_sea/sw5.JPG)  


### 스테이지 선택 화면

---

해당 스테이지를 클리어 해야만 다음 스테이지가 개방 된다.

![스테이지 선택 화면1](/assets/img/projects/going_to_the_sea/sw6.JPG)  


### 게임 플레이 화면

---

![게임 플레이 화면 1](/assets/img/projects/going_to_the_sea/sw7.JPG)  

화면 상단의 Start와 Goal은 해당 스테이지의 진행 정도를, 화면 자측의 녹색 수직 바는 플레이어의 현재 체력 상태를 나타 낸다.

그 아래의 분홍색 수평 막대는 키 입력 시간의 타이밍을 알려주는 게이지로 게임을 시작하면 빨간색 게이지가 저 안에서 채워 지는데 이 빨간색 게이지가 지나는 위치에 해당 하는 버튼을 눌러 줘야 한다.



키 입력 순서가 반전된 상태에 빠진 모습  
![게임 플레이 화면 1](/assets/img/projects/going_to_the_sea/sw8.JPG)  

이런 경우 반전 회복 아이템이 나왔을때 모든 버튼을 퍼펙트로 맞춰 회복 할 수 있다.


키 입력 방향이 반전된 상태에서 키 3개를 미처 입력 하지 못해 체력이 3포인트 깍여 나간 모습  
![게임 플레이 화면 1](/assets/img/projects/going_to_the_sea/sw9.JPG)  

### 게임 오버 화면

---

체력 게이지가 모두 깍여 게임 오버가 된 모습  

![게임 오버 모습 1](/assets/img/projects/going_to_the_sea/sw_gameover.JPG)  


## 2. 프로그램 구조 개요

#define 매크로로 정의된 상수 값들을 통한 프로그램의 상태 변화를 활용하는 프로그램의 전체적인 골격 구조는 이전의 이니셜B나 초모롱마와 다를 것이 없기 때문에 이에 대한 설명은 생략 한다.

이 프로그램 구현에 있어 중요한 함수와 변수들은 개략적으로 다음과 같은 것들이 있다.

```c
#define STAGE1_MAX_PAGE 100 //한 화면 상에 한 번에 5개의 키 입력 목록이 나오며 이 5개는 페이지 단위로 묶여 있고 페이지 단위로 변경 된다. 이 경우 총 500개의 눌러야 할 버튼이 있는 것이다.
#define STAGE2_MAX_PAGE 150
#define STAGE3_MAX_PAGE 200
#define STAGE4_MAX_PAGE 250
#define STAGE5_MAX_PAGE 300
#define FINAL_STAGE_MAX_PAGE 400

struct KEY {
int nKeyImageCode;//키 이미지 구별 코드.
int nKeyCode;//대응되는 키 값.
//ex : 0 : 1번키(좌측 대각선 방향 손 이미지), 1 : 2번키(양손바닥 이미지)
bool Pressed;//버튼이 눌렸는지의 유무, 눌린 버튼 이미지 그리기 등에 참조 된다.
};

KEY InputList1[STAGE1_MAX_PAGE][5];
KEY InputList2[STAGE2_MAX_PAGE][5];
KEY InputList3[STAGE3_MAX_PAGE][5];
KEY InputList4[STAGE4_MAX_PAGE][5];
KEY InputList5[STAGE5_MAX_PAGE][5];
KEY InputList6[FINAL_STAGE_MAX_PAGE][5];
KEY (*pCurrentInputList)[5]; //스테이지 별로 대응 되는 키 리스트를 가리키는 2차원 배열에 대한 포인터.

int nPlayerLife;
int nLevelOfDifficulty;//게임 난이도

bool bReverseInput; //키입력 반전 유무
int nScore;//점수는 (nBadCount * 1) + (nGoodCount * 3) + (nPerfectCount * 5)로 계산되어 게임 클리어나 오버시 스코어 화면에 보여준다.
int nInvalidCount;//부적절하게 눌린 무효키, 5개 단위로 플레이어의 몫 1개와 스코어 포인트 2가 차감 된다.
int nMissCount;//완벽하게 놓쳐 버린 버튼 개수로 그 개수 만큼 플레이어의 몫이 차감 된다.
int nNotBadCount;//아슬아슬한 타이밍에 버튼을 누른 횟수
int nGoodCount;//양호한 타이밍에 버튼을 누른 횟수
int nPerfectCount;//완벽한 타이밍에 버튼을 누른 횟수
int nValidCount;//bad, good, perfect 상관 없이 입력이 유효 판정된 버튼 개수
int nCurrentInputGauge;//입력 게이지 현재 값
int nWidthOfInputGauge;//입력 게이지 너비
int nHeightOfInputGauge;
int nIncreaseSize;//입력 타임 게이지의 증가 크기, 게이지 증가 속도
int nInputGauge_X;//입력 게이지를 그리기 위한 x 좌표
int nInputGauge_Y;//입력 게이지를 그리기 위한 y 좌표
int nCurrentPage;//현재 입력중인 키 리스트의 페이지 번호
int nMaxPage;//현재 키 리스트의 최대 페이지

int nMaxCount;//클리어를 위해 유효 판정 나야 하는 버튼의 총 개수, 즉 목표 거리를 의미한다, 화면 좌측 상단의 거리 출력 및 게임 클리어 판정에 쓰인다.

int nWidthOfProgressBar;//게임 진행 정도를 나타내 주는 막대바 이미지의 너비
int nHeightOfProgressBar;

int nPushedKey; //플레이어가 현재 누른 키

void ChangeInputList(int p_nCurrentStage)//스테이지 별 키 리스트 변경
{
 switch(p_nCurrentStage)
 {
  case 0 : //스테이지1
   pCurrentInputList = InputList1; //현재 스테이지에서 사용할 입력 리스트를 스테이지1의 입력 리스트로 변경
   nMaxPage = STAGE1_MAX_PAGE;//스테이지1에서 사용할 입력 리스트의 최대 페이지 개수
   nIncreaseSize = 1;//입력 게이지 증가 속도
   break;

  case 1 : pCurrentInputList = InputList2;
   nMaxPage = STAGE2_MAX_PAGE;
   nIncreaseSize = 1;
   break;
  
  case 2 : pCurrentInputList = InputList3;
   nMaxPage = STAGE3_MAX_PAGE;
   nIncreaseSize = 2;
   break;
  
  case 3 : pCurrentInputList = InputList4;
   nMaxPage = STAGE4_MAX_PAGE;
   nIncreaseSize = 2;
   break;
  
  case 4 : pCurrentInputList = InputList5;
   nMaxPage = STAGE5_MAX_PAGE;
   nIncreaseSize = 3;
   break;
  
  case 5 : pCurrentInputList = InputList6;
   nIncreaseSize = 4;
   nMaxPage = FINAL_STAGE_MAX_PAGE;
   break;
 }

 nMaxCount = nMaxPage * 5;//스테이지 별 이동 거리(진행 상태)는 스테이지별 전체 버튼 개수와 같다.
}


void ProcessInputGauge_CheckInput()//입력 게이지 바 계산 및 버튼 입력 판정
{
 int nTempGauge;//아래 유효 판정 범위 검색에서 좌표 전체 값을 가지고 연산 하는걸 피해 20(0~19 범위)의 크기로 간단하게 계산 하기 위한 임시 변수
 int nCurrentInputGaugeRate;//입력 게이지바를 %로 환산한 값을 저장하는 변수
 int nButtonIndex;//현재 입력 게이지바가 지나고 있는, 눌러야 할 버튼의 페이지내의 인덱스
 
 nCurrentInputGauge += (nIncreaseSize * nLevelOfDifficulty);//게이지는 난이도에 따라 증가 속도에 차이가 있다.

 if(nCurrentInputGauge > nWidthOfInputGauge)//입력 게이지 바가 최대 크기를 넘어서지 않게 한다.
  nCurrentInputGauge = nWidthOfInputGauge;

 nCurrentInputGaugeRate = ((float)nCurrentInputGauge / nWidthOfInputGauge);//입력 게이지 바의 진행 상태를 %로 환산하여 구한다.

 //버튼이 총 5개 배열 되어 있으므로 버튼 하나 당 전체 진행 정도의 20%의 비율을 갖는다. 이걸 이용해 현재 눌러야 할 버튼의 배열 번호를 얻어낸다.
 nButtonIndex = 20 / nCurrentInputGaugeRate; 
 
 nTempGauge = nCurrentInputGauge - (nCurrentInputGaugeRate * 20); //현재 입력 게이지의 값을 0~19 단위로 환산 하여 범위를 체크하여 퍼펙트, 베드 등의 판정에 이용함.

 if(pCurrentInputList[nCurrentPage][nButtonIndex].nKeyCode == nPushedKey)//현재 입력해야 하는 키와 누른 키가 동일 하다면 유효 범위의 등급 판정에 들어 간다.
 {
  if((nTempGauge >= 0 && nTempGauge <= 2) || (nTempGauge >= 17 && nTempGauge <= 19))//낫 베드
  {
   nNotBadCount += 1;
   nValidCount += 1;
  }
  else if((nTempGauge >= 3 && nTempGauge <= 7) || (nTempGauge >= 12 && nTempGauge <= 16))//굿
  {
   nGoodCount += 1;
   nValidCount += 1; 
  }
  else if(nTempGauge >= 8 && nTempGauge <= 11)//퍼펙트
  {
   nPerfectCount += 1;
   nValidCount += 1;
  }
  else//버튼 놓침
  {
   nMissCount += 1;
  }
  pCurrentInputList[nCurrentPage][nButtonIndex].Pressed = true;//버튼 눌림으로 체크, 이는 눌린 버튼들을 화면에 그릴때 참조 된다.
 }
 else//전혀 상관 없는 키를 눌렀다면
 {
  nInvalidCount += 1 * nLevelOfDifficulty;//무효타수를 난이도에 맞추어 가산 한다.

  if(nInvalidCount >= 5)//만약 이상한 키를 5번 이상 눌렀다면
  {
   nPlayerLife -= 1 * nLevelOfDifficulty; //플레이어의 몫을 난이도에 맞추어 감산 한다.
   nInvalidCount = 0;
  }
 }

 //입력 순서 반전의 경우에는 이미지 출력부에서 관장 한다.
 //입력 순서 반전을 하더라도 이미지 표시가 반대로만 보여질 뿐 실제로는 진행 값 증가 방향과 입력 순서와는 아무런 관련도 없고 따라서 변화 또한 없기 때문이다.
}

void ProcessTheProgressOfStage()//이동거리(현재 스테이지의 진행 정도) 계산
{
 int nDistanceRate = (((float)nValidCount / nMaxCount) * 100); //1. 클리어를 위한 목표 까지의 진행 정도를 백분율로 환산 한다.
 int nProgressRate = ((float)nDistanceRate / 100) * nWidthOfProgressBar;//2. 이를 다시 게임 진행바 너비에 대한 비율로 환산 한다.
 //스테이지_진행바_및_현재_위치_출력(nProgressRate); //진행바의 좌표 + 적정 비율로 환산된 수치 값을 활용 하여 스테이지 진행 상황을 출력 한다.
}
```