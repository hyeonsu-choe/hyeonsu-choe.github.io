---
title: Legend Of Uliyana
description: Windows 환경에서 SDL 1.2와 Win32 API를 이용하여 C++로 구현된 마우스 컨트롤 지원의 2D 액션 롤플레잉 게임
author: hyeonsu-choe
date: 2014-01-05 08:57:52 +0900
last_modified_at: 2025-10-26 22:30 +0900
categories: [Projects, Windows]
tags: [personal, win32, cpp, sdl, game, rpg]
toc: true
pin: false
render_with_liquid: false
image:
  path: /assets/img/projects/legend_of_uliyana/boss3.png
  # lqip: 
  alt: Gameplay screenshot of Legend Of Uliyana
---

| 구분 | 내용 |
|:----|:----|
|플랫폼|Windows|
|개발 도구 및 사용 언어|MS Visual Studio 2010, C++, SDL Library, Win32 API|
|개발 기간| 2012년 10월 ~ 2013년 11월 |
|저장소| [바로 가기](https://github.com/hyeonsu-choe/uliyana-suite)|

## 1. 게임 소개

마우스로 조작하는 싱글 액션 RPG.

필드를 돌아 다니며 몬스터들을 사냥하여 레벨과 장비를 업그레이드 시키고 이를 바탕으로 최종 보스를 죽이면 엔딩 화면이 나오며 끝나는 간단한 진행 구조를 갖추고 있다.

게임내 사용되는 모든 리소스들은 외부의 리소스 정보 리스트 파일과 내부의 리소스 테이블을 통해 관리 되도록 구현 하였다.

특히 게임 내 출력 텍스트들의 경우 언제든 외부의 스크립트 파일만 변경 하면 출력 언어를 원하는 형태로 변경 할 수 있다.

또한, 시스템 함수의 사용을 완전 배제하고 오로지 C++과 ANSI 표준함수들, 그리고 SDL 라이브러리만을 사용하여 제작 하였기 때문에 안드로이드 플랫폼으로의 이식이 용이할 것으로 보인다.

한편, 게임내 캐릭터들의 공격 모션이나 사망 시의 애니메이션들이 제대로 구현 되어 있지 않아 상당히 밋밋한 모습을 보이는데, 이는 별도의 추가적인 그래픽 편집 작업이 필요한 일이었기 때문에 아예 생략하고 넣지 않았기 때문에 그렇게 된 것이다.

게임 내에 사용된 모든 사운드 및 그래픽 리소스들은 일본 게임사 falcom의 대표작인 YS의 BGM과 RPG게임 만들기 커뮤니티의 것들을 가져와 사용 하였으며 이를 제외한 나머지 리소스들은 직접 자체 제작한 것들이다.

본래 이 게임은 한글판으로 제작 되었었다.

그러나 펜팔 친구가 이 게임을 플레이 해보길 원했었기 때문에 그를 배려하는 차원에서 사용 언어를 한글에서 영문으로 바꾸고 스토리 또한 그와 연관된 것으로 변경 하였다.

그러면서 게임 제목도 그 친구의 이름을 따서 리네이밍 하게 되었다.

### 게임 플레이 영상
---

[![게임 플레이 풀 영상](https://img.youtube.com/vi/CslrAYTZtX8/0.jpg)](https://youtu.be/CslrAYTZtX8)

### 프롤로그
---

![프롤로그 화면](/assets/img/projects/legend_of_uliyana/prologue.png)

```text
Once upon a time,

there was a pretty girl in "Ukraine Kingdom".

Oops, sorry~!!

I will check that.

Whether or not she was literally a pretty girl is vague for now.

Anyway,

Her name was "Uliyana".

She had fiery red hair and perfect swimming skill like a salmon.

Her swimming skill was so perfect that no one could match her in swimming.

One day,

A wicked lord came to "Simferopol" where she had been living.

No sooner had he arrived her town than strange things happened.

Monsters started to emerge in the forest near the town.

Many people were wounded by the monsters.

"Uliyana" thought,

"the wicked lord must be to blame for this disaster."

She wanted to save people in need and thought how to help them.

Fortunately,

She was good at on the use of weapons as much as her swimming skill.

She decided to defeat him on her own.

So She just took her first step into the forest.

It is the tale of her adventure.

A lot of people are calling it...

"Legend of Uliyana"
```

### 시작 메뉴 화면
---
![시작 메뉴 화면](/assets/img/projects/legend_of_uliyana/menu.png)  

### 게임 시작
---
![플레이 시작 화면](/assets/img/projects/legend_of_uliyana/start.png)  

처음 게임을 시작하면 필드 한쪽에 단검과 옷이 떨어져 있는데 이를 주워 게임을 진행하면 된다.

조작법은 간단하다.

이동, 공격, 아이템 줍기 등의 게임 내 캐릭터 조작 명령은 모두 마우스 왼쪽 버튼을 조작하여 이루어 지며

아이템 장비나 메뉴 선택 등 UI 관련된 명령은 모두 마우스 오른쪽 버튼을 조작하면 된다.

#### [조작법]

| 동작 | 방법 |
|:--- |:---: |
|이동|이동하길 원하는 위치에 마우스 커서를 올려 놓고 마우스 왼쪽 버튼 클릭|
|공격|공격할 대상 몬스터 위에 마우스 커서를 올려 놓고 마우스 왼쪽 버튼 클릭|
|아이템 줍기|줍기 대상 아이템 위에 마우스 커서를 올려 놓고 마우스 왼쪽 버튼 클릭|
|UI 조작| 선택 및 원하는 메뉴 버튼 위에 마우스 커서를 올려 놓고 마우스 오른쪽 버튼 클릭 |

<br>

화면 중앙 하단의 빨간색 막대는 플레이어 캐릭터의 현재 체력 상태를, 파란 막대는 마력 상태를 나타낸다.

그 위의 초록색 막대는 플레이어 캐릭터의 공격 딜레이 시간을 나타낸다.

그 옆의 파란색 하트는 플레이어의 몫으로 플레이어가 한 번 사망 할 때마다 하나씩 소모 되어진다.

플레이어는 사망할 때마다 시작점에서 부활을 하게 되며 파란색 하트를 모두 소진한 경우 게임 오버가 된다.

이 파란색 하트는 필드에 배치 되어 있는 빨간색 하트 아이템을 먹으면 1회씩 회복이 가능 하다.

이 파란색 하트는 아주 낮은 확률로 몬스터가 드랍 하기도 한다.

#### [HUD]

| 요소 | 의미 |
|:---|:---|
|빨간 막대|HP|
|파란 막대|MP|
|초록 막대|공격 딜레이 시간|
|파란 하트| 라이프 (사망 시 1개 소모)|

### 상점 기능
---
![상점 메뉴 화면](/assets/img/projects/legend_of_uliyana/store.png)  

보통 필드의 몬스터들을 처치 하면 랜덤으로 아이템들을 드랍 하는데 그 확률이 낮게 설정 되어 있다.

그렇기 때문에 몬스터 사냥을 통해 돈을 모으거나 드랍된 장비들을 팔아 좀더 성능 좋은 장비로 업그레이드 해나갈 수 있도록 게임 시작 지점에 상점이 존재 한다.

장비창의 장비들은 마우스 드래그 앤 드롭으로 서로 위치를 옮겨 배치를 변경 할 수 있으며 장비창 밖으로 드래그 앤 드롭을 할 경우 이를 파괴 할 수 있다.

### 몬스터와 아이템 드롭
---
![몬스터 사냥 화면](/assets/img/projects/legend_of_uliyana/hunt.png)  

몬스터들의 레벨과 종류에 따라 드롭되는 아이템들의 목록 또한 각기 다르며 죽은 몬스터들은 일정 시간이 지난후 재생성 된다.

몬스터의 드롭 아이템들 목록과 확률은 별도의 데이터 파일에 저장 되어 관리 된다. 

### 보스 몬스터
---
필드에는 4명의 필드 보스 몬스터들이 존재한다.  

이들을 모두 처치 하면 4대 무기를 얻을 수 있다.  

보스 모스터들의 재생성 시간은 일반 몬스터들의 재생성 시간 보다 길다.

#### [보스 보상 표]

|보스|보상 (4대 무기)|
|:---|:-------------|
|가고일|고대활|
|광신도|암흑의 호른|
|거대 골렘|성기사의 검|
|크림 드래곤|신의 은총|

첫번째 필드 보스인 가고일을 처치하면 궁극의 4대 무기중 하나인 고대활을 얻을 수 있다.
![보스1 가고일과의 전투 화면](/assets/img/projects/legend_of_uliyana/boss1.png)  
![보스1 아이템 드롭 화면](/assets/img/projects/legend_of_uliyana/boss1_dropitem.png)  

두번째 필드 보스인 광신도를 처치하면 궁극의 4대 무기 중 하나인 암흑의 호른을 얻을 수 있다.
![보스2 가고일과의 전투 화면](/assets/img/projects/legend_of_uliyana/boss2.png)  
![보스2 아이템 드롭 화면](/assets/img/projects/legend_of_uliyana/boss2_dropitem.png)  

세번째 필드 보스인 거대 골렘을 처치하면 궁극의 4대 무기 중 하나인 성기사의 검을 얻을 수 있다.
![보스3 가고일과의 전투 화면](/assets/img/projects/legend_of_uliyana/boss3.png)  
![보스3 아이템 드롭 화면](/assets/img/projects/legend_of_uliyana/boss3_dropitem.png)  

필드의 마지막 보스인 크림 드래곤을 처치하면 궁극의 4대 무기 중 하나인 신의 은총을 얻을 수 있다.
![보스4 가고일과의 전투 화면](/assets/img/projects/legend_of_uliyana/boss4.png)  
![보스4 아이템 드롭 화면](/assets/img/projects/legend_of_uliyana/boss4_dropitem.png)  

### 포탈과 던전 입장
---
![포탈 생성 화면](/assets/img/projects/legend_of_uliyana/boss4_portal.png)  
모든 필드의 보스들을 1회 씩 죽이면 던전으로 입장할 수 있는 포탈이 생성 된다.

이 포탈은 보스 몬스터들을 처치한 순서와는 상관 없이 마지막으로 처치한 보스 몬스터 근처의 비석 중 한 곳에 생성 된다.

이 포탈을 통해 게임의 최종 보스인 사악한 영주가 있는 던전에 입장 할 수 있다.

던전에 진입하면 던전을 위한 BGM 곡으로 변경 된다.

던전 내에는 필드 보다 더 강한 몬스터들이 배치 되어 있으며 던전 구석 구석에 궁극의 4대 무기들이 숨겨져 있다.

![던전 입장 화면](/assets/img/projects/legend_of_uliyana/dungeon_entrance.png)  

### 최종 보스
---
최종 보스인 사악한 영주 근처에 도달하면 BGM이 보스전 곡으로 변경 된다.

만약 보스와 거리를 떨어 뜨려 전투 상태가 해제되면 원래의 던전 BGM으로 곡이 변경 된다.

사악한 영주를 처치하고 조금 기다리면 에필로그 화면으로 넘어가며 게임이 종료되며 시작 메뉴 화면으로 돌아가게 된다.
![최종 보스와의 전투 화면](/assets/img/projects/legend_of_uliyana/final_boss.png)  

![final_boss](/assets/img/projects/legend_of_uliyana/final_boss_killed.png)  

### 에필로그
---
![에필로그 화면](/assets/img/projects/legend_of_uliyana/ending.png)  

![최종 엔딩 화면](/assets/img/projects/legend_of_uliyana/fin.png)  

#### [에필로그 스크립트]
```text
Eventually, "Uliyana" defeated the wicked lord.

By the way,

It turned out that her suspect was wrong.

The wicked lord didn't commit the crime

Yes,

"Uliyana" attacked the innocent victim!!

She became most famous criminal in "Simferopol"

In a sense,

She left her name in history of "Ukraine Kingdom".

Two days later,

"Uliyana" ran away.

Yes, that is true of "Legend of Uliyana".

<FIN>
```


## 2. 프로그램 구조 및 구현

#### [클래스 다이어그램]
다음은 프로그램 전체의 구조를 클래스 다이어그램으로 도식화한 것이다.
![클래스 다이어그램](/assets/img/projects/legend_of_uliyana/LOUUML.png)  


프로그램은 클래스 CGame_Engine의 인스턴스 cGame을 컨테이너로 삼아 실질적인 기능들을 수행하는 멤버 함수들을 호출 하는 방식으로 실행 되는 구조다.

이 멤버 함수들은 CGame_Engine 내에서 엑세스 되는 게임 상태 객체의 멤버 함수들의 호출 명령문들을 감싸는 Wrapper의 역할을 한다.

프로그램의 로직은 하나의 루프문을 돌면서 이 Wrapper 함수들을 호출하는 형태로 구성 되어 있으며, 게임의 실행 상태가 바뀌면 그로 인해 Wrapper 안에서 호출 되는 함수들 또한 변경 되어지는 형태로 구현 되었다.

```
프로그램 시작
while(프로그램 종료 여부)
{
  키 입력 처리();

  필요한 데이터들 갱신();

  그래픽 처리();

  게임의 FPS 제어();

  화면 최종 출력();
}
프로그램 종료
```

위의 Wrapper 함수들과 프로그램의 종료 여부 등을 관장 하는 클래스 CGame_Engine 내부에는 게임의 실행 상태를 결정하고 전이 시키는 하나의 스택 자료구조 객체가 내장되어 있다.

### 프로그램의 상태 전이
---
프로그램은 실행 상태(인트로, 메뉴, 게임 플레이, 서브 메뉴, 게임 오버, 엔딩 실행 상태)에 따라 각기 다른 로직으로 구현 되어 있는 함수들을 내부에서 호출하며 상황에 맞게 그 상태를 제어한다.

이러한 내부의 상태 변화는 State_Stack 이라고 이름 붙여진 스택 자료구조를 통해 구현 되었으며 스택의 최상층인 Top의 위치에 오는 상태 값이 곧 프로그램의 현재 상태가 되는 방식이다.

이 스택은 CGame_Frame에 대한 포인터를 자료형으로 하며 내부에 여러 게임 상태 객체들에 대한 주소를 쌓아 놓고 pop과 push 연산, 그리고 top을 참조하는 방식을 통해 게임 상태를 인트로 화면이나 메뉴 화면, 실제 게임 화면 상태 등으로 유기적으로 전이 시킨다.

예를 들어, 게임 도중에 서브 메뉴를 띄웠다가 이를 종료하고 다시 게임을 플레이 하는 경우 스택 내부적으로는 현재 상태인 게임 플레이 상태 위에 서브 메뉴 상태를 Push 하였다가 다시 Pop 하는 일련의 연산을 수행 하게 되고 이에 따라 게임의 상태는 게임 플레이 -> 서브 메뉴 -> 게임 플레이와 같이 흐르게 되는 것이다.

#### [게임 플레이 → 서브 메뉴]  
![상태 전이1](/assets/img/projects/legend_of_uliyana/state_stack2.png)

Push() 연산을 통해 스택에 새로 push되어 Top에 위치하게 된 상태 객체는 그 멤버 함수인 Init() 함수의 호출을 통해 필요한 데이터들을 초기화 시킨다.

#### [서브 메뉴 → 게임 플레이] 
![상태 전이2](/assets/img/projects/legend_of_uliyana/state_stack.png)

Pop 연산시에는 필요에 따라서 현재 Top에 있는 상태 객체의 리소스들을 해제하는 Cleanup() 작업을 수행 한다.

### 프레임 제어
---

CPU의 빠른 연산 처리 속도로 인해 게임의 진행 속도가 본래 게임에 맞지 않게 빠르게 진행 되는 현상들을 막고 게임내 그래픽들의 원활한 애니메이션 처리를 위해 FPS 조절 기법을 사용 하였다.

![프레임 제어](/assets/img/projects/legend_of_uliyana/fps_exlpain.png)

FPS는 위의 그림과 같이 Sleep() 함수 호출을 통해 게임의 쓰레드나 프로세스 자체를 대기 상태로 전환 시킴으로써 조절 된다.

이를 요약하면 다음과 같다.

```
Sleep(목표 FPS를 맞추기 위해 필요한 루프 1순회 당 대기 시간);
```

예를 들어,  
FPS 값으로 30이 주어질 경우, 프로그램은 초당 30 프레임의 실행 속도를 유지하기 위해 루프를 한 번 순회 할 때마다 약 33ms 씩, 60 FPS인 경우에는 약 16ms 씩의 시간이 소요 되도록 Sleep() 함수에 의해 대기 상태에 빠짐으로써 지연이 강제 되는 것이다.

물론 for문 같은 반복문들을 사용하여 busy waiting 방식으로 프로세스의 진행을 지연 시키는 방법도 생각해 볼 수 있겠지만 그러한 방식은 필요한 대기 시간을 정확하게 예측하기에도, 적용하기에도 어려울 뿐더러 해당 프로세스로 하여금 다른 프로세스들에게 실행 시간을 양보 하지 않고 시스템의 자원을 독점하게 함으로써 시스템 자원 활용 측면에서 효율성을 떨어 뜨리는 문제가 있으므로 가급적이면 지양 하는 편이 좋을 것이다.

여기에 더해,  
이 프로그램은 프로세스가 장시간의 Blocked 상태에 빠졌다가 회복 되는 등의 여러가지 이유로 FPS가 정해진 FPS 값 보다 낮아 졌을때 이를 회복 하기 위한 Frame Skipping 기법을 사용하고 있는데 이 게임 자체가 캐릭터의 걷기 애니메이션을 제외하고는 이동이나 공격 실행 등의 모든 게임내 동작들이 FPS에 상관 없이 시간차 유무를 통해 이루어지도록 구현 되어 있어 단순히 캐릭터의 보행 애니메이션 재생 속도 정도만 조절 될 뿐이다.

다음은 실제 사용된 코드다.

```cpp
 ElapsedTicks += (SDL_GetTicks() - LastTicks);

 if(ElapsedTicks < Interval)
 {
  SDL_Delay(Interval - ElapsedTicks);
  ElapsedTicks = 0;
 }

 else
 {
  ElapsedTicks -= Interval;
 }

 LastTicks = SDL_GetTicks();
```

이렇게 프로그램의 전체적인 속도가 FPS에 맞게 동기화된 이후 부터는 본격적으로 게임 캐릭터 등과 같은 게임내 오브젝트들의 에니메이션 처리를 진행 한다.

예를 들어 30 FPS로 동작하는 프로그램에서 캐릭터의 어떤 한 동작을 표현 하기 위해 준비된 이미지가 단 4장 뿐이라면, 프로그램 로직의 루프문을 한 번 순회 하는데 약 33ms씩의 대기 시간이 필요 하다는 점을 감안하여 루프 순회 7~8회를 주기로 하여 이미지를 한 장씩 교체해 나가는 방식으로 애니메이션을 처리 한다.

이에 대한 처리 과정은 다음과 같다.

```
루프 순회 횟수 = (루프 순회 횟수 + 1) % 이미지 교체 주기

if(루프 순회 횟수 == 0)
  이미지 번호 = (이미지 번호 + 1) % 전체 이미지 개수

이미지 출력(이미지 번호);
```  

### 리소스 관리
---

![리소스 관리 및 사용 흐름도](/assets/img/projects/legend_of_uliyana/Resource_Flow.png)

게임내에서 사용되는 리소스를 관리, 활용하기 위해 프로그램의 흐름은 다음과 같이 A와 B 파트로 나누어져 선형적으로 흐르도록 구현 되었다.

- **A 과정** :  
파일의 경로와 이름, 구성 정보 등이 채워져 있는 외부의 리소스 리스트 파일을 읽어 들여 하나의 해쉬 테이블을 구성한다.  
즉, 리소스 정보를 프로그램내에 구축하는 과정이다.

- **B 과정** :  
게임 실행 중 실질적인 리소스 로드 작업이 필요할 때마다 A 과정에서 구성된 테이블을 참고하여 외부의 리소스들을 읽어 들인다.  
즉, 구축된 리소스 정보를 활용하여 리소스를 읽어 들이고 사용하는 과정이다.

A과정은 처음 프로그램이 구동될때 진행 되고,

B과정은 위 그림상에서 보이는 헤시 테이블의 구성이 끝난 뒤에 진행 된다.

#### Resource List File
---
리소스 리스트 파일안에는 게임 내에서 사용 되는 리소스 정보들이 다음과 같은 형태로 저장 되어 있으며 이는 행 단위로 읽어 들여져 분석 된다.

```cpp
img("./Image/Intro.png", 1, Intro_State, 32, 37)

txt("Ending.dat", Ending_State, page, 3000, on, 10, 0, 100)

btn("./Interface/OFF_Exit_Game.png", "./Interface/ON_Exit_Game.png", "./Interface/OVER_Exit_Game.png", 0, Menu_State, 313, 440)

snd("./Sound/click1.wav", wav, 0)
```

각각의 행은 파일의 경로와 이름, 이미지 파일일 경우에는 프레임 개수, 리소스가 사용될 모드, 출력될 x, y좌표, 텍스트일 경우 한 줄당 출력 대기 시간, 출력 방식 등의 정보를 문자열의 형태로 나타낸 것이다.

#### Parsing
---
리소스 리스트 파일로 부터 행 단위로 읽혀진 문자열들은 게임내에서 사용 할 수 있는 의미있는 속성, 데이터 단위로 파싱되어 각각 Queue에 Enqueue 된다.

이렇게 Queue에 저장된 데이터들은 다시 하나씩 Dequeue 되어 게임 내에서 실제로 사용할 수 있게끔 CResource_File_Record 자료구조 단위로 가공 처리 된다.

이때 해당 자료 구조에 담기는 데이터의 단위는 리소스 리스트 파일로 부터 읽어 들인 하나의 행이다.

#### CResource_File_Record
---
다음은 CResource_File_Record의 내부 구성이다.

```cpp
 int nType; //데이터 타입(이미지(프레임에 의해 캐릭터와 배경 등 구분),텍스트,브금,효과음,타일)

 char* pFile_Name;

 char* pFile_Name2;

 char* pFile_Name3;

 int nApply_Mode;

 int nFrame;//프레임 값 //캐릭터 이미지에 씀 // 버튼:ON or OFF

 int nGroup_Num;//버튼:그룹번호 //텍스트:Page or Scroll

 int nOption1;//부과 옵션1(General Variable)

 int nOption2;//부과 옵션2(범용 변수)

 CCoord Coord;

 int nLayer_Num;
```
Queue로부터 읽어들일 데이터가 없을 때까지 데이터를 읽어들이면서 이들을 각각의 데이터 타입에 맞게 분류하여 저장 한다.

이렇게 CResource_File_Record 단위로 묶여 가공된 데이터들은 Queue에서 읽어 들이는 작업이 종료되는 즉시 헤시 테이블에 등록 되어 진다.

그리고 외부의 리소스 리스트 파일에서 더이상 읽어들일 데이터가 없을 때까지 지금까지의 과정인 A과정 전체를 반복 수행 하면서 헤시 테이블을 구성한다.

#### Resource_Table(Hash Table)
---

![리소스 테이블](/assets/img/projects/legend_of_uliyana/Hash_Table.png)

① 열 검색 : 헤시 테이블은 Hash_Entry라는 자료구조 타입의 배열을 속성 값으로 사용하며 이 Hash_Entry의 멤버 데이터인 single linked list 자료구조의 노드를 튜플 값으로 사용한다.

② 행 검색 : 튜플들은 속성의 리스트를 순환하면서 Key 값을 비교하는 작업을 통해 검색 된다.

③ 실질적인 데이터에 접근 : Key 값을 가지고 있는 노드들 또한 리스트를 가지고 있는데 이 리스트에 연결 되어 있는 노드들이야말로 해쉬 테이블에서 실질적으로 참조해야 하는 데이터들이다.

이들은 CResource_File_Record 자료구조를 노드로 하여 선형으로 연결되어 있다.

그렇기 때문에 일치하는 Key 값이 테이블에 존재하는 경우에는 이 Key값을 가지고 있는 노드의 리스트에 연결 되어 있는 모든 노드들을 읽어 들인다.

다음은 헤시 엔트리와 헤시 테이블의 클래스 정의문이다.


```cpp
template<class Keytype, class Datatype>//해쉬 테이블에서 column 데이터로 쓰임

class Hash_Entry{

public:

 Keytype Key;

 SLinkedList<Datatype> Data;
};

template<class Keytype, class Datatype>//기본적인 해쉬 테이블 자료구조의 개량형

class CResource_Hash_Table{

public:

 Array<SLinkedList<Hash_Entry<Keytype, Datatype>>> Hash_Table;

 int nSize; // 해쉬 테이블 사이즈

 int nCount; //해쉬 테이블내에 저장된 자료수

 unsigned long int (*pHash_Func) (Keytype&);//함수 포인터

 CResource_Hash_Table();

 CResource_Hash_Table(int, unsigned long int (*pHash_Func) (Keytype&));

 ~CResource_Hash_Table();

 void Set_pHash_Func(unsigned long int (*pHash_Func) (Keytype&));//함수 포인터 세팅 및 변경

 void Resize(int);

 Hash_Entry<Keytype, Datatype>* Find(Keytype); //튜플 검색

 void Insert(Keytype, Datatype); // 삽입

 bool Remove(Keytype Param_Key); // 제거
};
```

#### Resource_Table(헤시 테이블) 참조
---

헤시 테이블에 데이터를 삽입하거나 그로부터 데이터를 검색할 때에는 리소스의 데이터 타입(TYPE)과 리소스가 적용될 모드(Apply mode)를 조합하여 검색을 위한 키 값으로 사용한다.

리소스의 테이터 타입과 리소스가 적용될 모드는 보다 쉽게 조합키를 생성할 수 있도록 다음과 같이 16-진수로 구성되어 있다.

```cpp
//Apply mode

//사운드는 모든 상태에서 재생 되므로 따로 상태를 가질 이유가 없다.
#define Intro_State  0x0001

#define Menu_State  0x0002

#define Game_State  0x0003

#define GameOver_State 0x0004

#define SubMenu_State 0x0005

#define Ending_State 0x0006

//Type
#define SND  0x00000000 //사운드

#define IMG  0x00000100 //이미지

#define TXT  0x00000200 //텍스트

#define BTN  0x00000300 //버튼

#define TILE 0x00000400 //타일

#define OBJ  0x00000500 //오브젝트
```
이 두 속성 값은 (Type | Apply mode) 또는 (Apply mode | Type) 와 같이 OR 연산을 통해 조합 되어 하나의 키 값을 만들어낸다.

하위 8비트에는 Apply mode 값이, 상위 8비트에는 Type 값이 위치하게 되는 것이다.

예를 들어, 엔딩 모드에서 사용 되어지는 이미지 파일을 참조하는 경우에는 Ending_State | IMG 연산을 통해 0x0106 값으로 조합 되어진다.

이렇게 조합된 키 값은 다음과 같이 사용 된다.

##### [테이블에 데이터삽입 시] 
```cpp 
Resource_Table.Insert(Data.IsApply_Mode() | Data.IsType(), Data);
```

##### [테이블에서 데이터 검색 시]
```cpp
pGame_Eng->cResource_DB.Resource_Table.Find(TXT | Intro_State);
```

사실, 삽입과 검색 함수의 인수 값으로 조합키를 입력 하도록 구현한 이유는 테이블의 관리와 사용상의 편의를 위해서 였다.

그렇기 때문에 이렇게 입력된 조합키는 함수 내부에서 비트 마스크 작업을 수행하는 아주 간단한 해쉬 함수에 의해 hashing 되어져 속성 값과 튜플 검색을 위한 키 값으로 다시 분해 된다.

#### 실질적인 리소스 로딩 작업
---

헤시 테이블 구성이 완전히 끝나고 나면 이 헤시 테이블은 이후 부터 일종의 리소스 정보 참조 테이블로써, CGame_Frame을 공통 부모로 하는 Cxxxx_State 객체들에 의해 사용된다.

Cxxxx_State 객체들은 게임 내부의 실행 상태가 변경 되어지면 해당 모드에서 필요한 리소스들을 Find(TXT | Intro_State)와 같은 함수 호출을 통해 읽어들인다.

이때 테이블로 부터 얻는 값은 Key 값과 일치하는 Key를 가진 튜플의 주소 값인데, 이 튜플 데이터 안에는 하나의 리스트가 들어 있다.

이 리스트는 CResource_File_Record 형식의 실질적인 리소스 데이터들의 리스트이다.

Cxxxx_State 객체들은 바로 이 리스트를 끝까지 읽어 들인 후에 이들 값을 동일한 데이터 형태(CResource_File_Record 타입)로 하는 내부 리스트에 차곡 차곡 저장해 놓고 사용한다.

물론 각 Cxxx_State 객체들은 테이블에서 얻은 정보를 따로 내부에 보관해 놓고 관리하기 때문에 처음을 빼고는 다시는 참조하지 않는다.

이는 상태 전이시 호출되는 초기화 함수 내부에서 테이블을 참조한 적이 있는지 없는지를 내부 상태 변수에 마킹해 놓고 확인하는 방식으로 구현 된다.

이렇듯 파일명을 통해 직접적으로 읽어들이는 방법을 굳이 사용하지 않고 일견 복잡해 보이고 시간도 더 걸릴 것 같아 보이는 위와 같은 방법을 사용하는 이유는 바로 시스템 자원을 보다 효율적으로 관리하고 게임 컨텐츠의 유지 보수를 좀더 유기적으로 하기 위해서다.

### 맵 스크롤 구현
---

본 게임은 기본적으로 타일맵 방식을 체택하고 있으며 맵의 크기가 몇이 되었든 플레이어의 캐릭터를 화면의 정중앙에 오도록 배치하여 이를 중심으로 맵을 종횡으로 움직이는 방식으로 구현 되었다.

또한 화면의 중심에 위치한 플레이어 캐릭터를 기준으로 하여 화면에 표시되는 영역만 출력 하고 그 외의 영역은 출력하지 않음으로써 그래픽 메모리와 데이터 버스 그리고 그리기 작업 수행 시간과 같은 시스템 자원들을 절약 한다.

#### 플레이어 캐릭터의 화면 중앙 배치
---

플레이어 캐릭터는 게임내 전체 맵을 돌아 다닌다.

그런데 보통 전체 맵의 크기가 출력 화면을 넘어설 만큼 더 넓은 경우가 많기 때문에 전체맵 상의 좌표와 출력 화면 상의 좌표는 항상 일치하지 않는다.

전체 맵과 출력 화면의 좌표 범위를 살펴 보면, 전체 맵의 넓이가 1600 * 1200인 경우, x축 좌표 범위는 0~1599, y축 좌표 범위는 0~1199이고 출력 화면의 넓이가 800 * 600인 경우 x축 좌표 범위는 0 ~ 799, y축 좌표 범위는 0~599 이며 화면 중앙의 좌표 값은 (399, 299) 또는 (400, 300)이 된다.

물론 정확한 중앙 좌표의 값은 (39.5, 299.5)이지만 현재 다루고 있는 픽셀 단위에서는 소수점 단위의 이미지를 표현하지 못하기 때문에 (399, 299)와 (400, 300) 중 어느 한쪽을 중앙 좌표 값으로 삼는 것이다.

여기서는 편의상 (400, 300)을 중앙 값으로 사용 한다.

어찌 되었든 위처럼 서로 표현 범위가 다르기 때문에 쌍방 범위의 차이로 인해 다음과 같은 문제가 발생 한다.

예를 들어, 플레이어 캐릭터가 전체 맵 상에서 좌표 (600, 400)에 위치하고 있는 경우 이 좌표값을 그대로 출력 화면에 사용해도 별다른 문제가 없다.

왜냐하면 출력 좌표 값이 출력 화면의 좌표 범위 안에 있기 때문에 화면 상에서도 같은 좌표값인 (600, 400) 위치에 출력 된다.

그러나 전체 맵 상에서 플레이어 캐릭터의 좌표가 (800, 700)인 경우에는 문제가 생긴다.

캐릭터의 좌표 값을 그대로 화면에 출력 하였다가는 캐릭터가 화면 밖에 그려져 유저에게는 보이지 않는 현상이 벌어지는 것이다.

이러한 문제를 해결하기 위해서는 화면 좌표계의 범위 밖으로 나가 있는 플레이어의 캐릭터를 화면내 범위, 이 경우에는 화면의 정중앙 까지 끌고 와서 고정 시켜야 하기 때문에 필연적으로 플레이어 캐릭터의 좌표 값을 화면 중앙 좌표 값으로 변경 시키기 위한 작업을 해야 한다.

이때 화면 중앙의 좌표는 항상 (400 300)으로 고정 되어 있고, 플레이어의 좌표는 게임상에서 시시각각 변하기 때문에 플레이어 캐릭터의 좌표 값을 변경 시키기 위한 작업이란 것은 결국엔 가변적인 속성을 갖는 일종의 변위 값(offset)을 구하는 작업이 된다.

이 offset을 구하는 방법은 다음과 같다.

먼저 전체맵 좌표계 상의 플레이어 캐릭터의 좌표(900, 700) 값을 화면 좌표계 상의 중앙 좌표(400, 300) 값으로 만들기 위해 더하거나 뺄 값을 찾는다.

이 값은 캐릭터의 좌표와 화면 중앙 좌표 사이의 거리인데, 플레이어 캐릭터의 좌표를 기준으로 했을때 이 변위 값은 (-500, -400)이 되고, 화면 중앙의 좌표를 기준으로 했을 때는 (+500, +400)이 된다.

이처럼 변위값(offset)의 부호는 중앙 좌표와 캐릭터 좌표 중 어느 한쪽을 기준으로 하여 연산을 수행하느냐에 따라 달라지게 된다.

변위값이란 절대 값이나 마찬가지이기 때문에 연산의 편의에 따라 offset을 차후에 더하는 식으로 이용 할땐 -부호로, 빼는 식으로 이용 할땐 +부호로 하여 사용 한다.

그리고 이 offset 값에 의해 캐릭터가 화면 중앙으로 당겨지게 되면 그만큼 전체 맵의 시작 좌표 (0, 0)에서 부터 출력 화면의 좌표 (0, 0) 사이에도 동일한 거리의 차이 값이 발생하는데 실제로는 이 값을 실질적인 offset으로 보는 것이 구현상 용이하다.

결국에는 여기서 구한 offset 값이 (-500, -400)일 경우 전체 맵 좌표 (0, 0)의 이미지는 출력 화면 좌표 (-500, -400)에 그려지게 되고, 전체 맵 좌표 (500, 400)의 이미지는 출력 화면 좌표 (0, 0)에 그려지게 된다.

이 offset을 구하는 과정을 공식화 하면 다음과 같다.

#### [방법 1: 플레이어 캐릭터 좌표를 기준으로 offset을 구하는 방법]
```
offset x = -캐릭터의 x좌표 + (출력 화면의 너비 / 2)

offset y = -캐릭터의 y좌표 + (출력 화면의 높이 / 2)
```

#### [방법 2: 화면 중앙 좌표를 기준으로 offset을 구하는 방법]
```
offset x = 캐릭터의 x좌표 - (출력 화면의너비 / 2)

offset y=  캐릭터의 y좌표 - (출력 화면의 높이 / 2)
```
offset 값은 캐릭터가 이동 하여 전체 맵 상에서의 좌표 값이 바뀔 때마다 새로 갱신 된다.

즉 캐릭터의 좌표 값이 (900, 700)일때의 offset 값 (-500, -400) 또는 (+500, +400)은 캐릭터의 좌표 값이 (901, 701)로 바뀌고 나면 의미가 없는 값이 되기 때문에 또다시 연산을 통해 새 좌표에 맞는 offset (-501, -401) 또는 (+501, +401)로 갱신 되는 것이다.

이렇게 끊임 없이 갱신 되는 offset 값 덕분에 캐릭터의 전체 맵 상에서의 좌표 값이 무엇이 되었든 캐릭터는 항상 화면의 정중앙에 위치하게 된다.

* offset 적용 전 :  
![before_appy_offset](/assets/img/projects/legend_of_uliyana/CalcuOffset0.png)

* offset 적용 후 :  
![after_appy_offset](/assets/img/projects/legend_of_uliyana/CalcuOffset1.png)

하지만 offset 값에 의해 화면 영역 밖에 출력 되는 이미지들은 메모리와 CPU 사용 시간 등 시스템 자원의 낭비를 낳는 주된 요인이 된다.

이를 예방하려면 전체 맵 상에서 화면에 출력해야 할 부분만을 따로 추려 내는 출력 유효 범위 계산 작업을 해야 한다.

#### 맵의 출력 유효 범위 계산
---

게임 내에서 다루어지는 전체 맵의 넓이는 출력 화면의 넓이 보다 훨씬 넓다.

따라서 출력 화면 범위내의 데이터들만 따로 선별해서 화면에 출력 되도록 한다면 그래픽 메모리와 데이터 버스 사용량 그리고 CPU 사용 시간 등을 절약 할 수 있게 된다.

이 출력 유효 범위 값은 플레이어의 현재 위치에서 화면의 절반 크기의 값 만큼을 감가산하는 방식을 통해 알아낼 수 있다.

이때 구하는 값은 범위 값이므로 x와 y에 대하여 각각 시작 좌표와 끝 좌표 모두가 계산 되어야 한다.

그런데 대개 2D 게임에서는 맵 구성이 타일 맵 방식으로 구현 되는 경우가 많다.

그렇기 때문에 캐릭터의 좌표와 타일 맵의 인덱스가 1:1로 대응하는 경우라면 위에서 구한 offset 값이 곧 출력 범위의 좌표를 의미 하는 것이 되지만, 만약 맵에 쓰이는 타일 1장당 너비가 40인 경우에는 캐릭터의 좌표 값 40당 타일이 1장씩 대응 되는 1:n 관계가 형성 되기 때문에 offset 값을 그대로 출력 유효 범위 값으로 사용 할 수가 없다.

이렇듯 1:n 관계가 형성되는 경우 캐릭터 좌표의 전체 범위와 맵의 전체 범위는 서로 분리되어 논리적으로만 연결된 상태가 된다.

따라서  맵의 출력 유효 범위는 좌표 단위가 아닌 맵의 타일 번호 단위가 되어야 한다.

이를 위해서는 캐릭터의 좌표 값과 화면의 중앙 값 또한 타일 맵의 번호 단위로 바꿔 줘야 하는데 이는 캐릭터의 좌표 값과 화면의 중앙 값을 각각 타일 맵의 너비로 나누어 구할 수 있다.

이를 통해 화면의 중앙 값은 화면 중앙 부터 각 끝단 사이에 위치한 맵 타일의 개수 값이 된다.

그리고 보통 타일 맵이나 픽셀 맵이나 그 형태는 모두 2D 배열이기 때문에 x축은 타일 배열의 열 번호로, y축은 행 번호로 삼으면 된다.

물론 1차원 배열로 구성하더라도 인덱스 값을 적절히 변환 시키면 문제 될 것이 없다.

이에 대한 공식은 다음과 같다.

#### [타일 인덱스 단위로 출력 범위 구하는 방법]
```text
x축 타일의 시작 열 번호 Min X : (캐릭터의 x좌표 / 타일의 너비) - (출력 화면의 너비 / 2 / 타일의 너비)

x축 타일의 끝 열 번호 Max X : (캐릭터의 x좌표 / 타일의 너비) + (출력 화면의 너비 / 2 / 타일의 너비)

y축 타일의 시작 행 번호 Min Y : ( 캐릭터의 y좌표 / 타일의 높이) - (출력 화면의 높이 / 2 / 타일의 높이)

y축 타일의 끝 행 번호 Max Y : (캐릭터의 y좌표 / 타일의 높이) + (출력 화면의 높이 / 2 / 타일의 높이)
```

예를 들어,  
플레이어의 캐릭터가 1600*1200(픽셀 단위) 크기의 맵 상에서 x = 900, y = 700 좌표상에 위치해 있으며 맵 타일의 사이즈가 40 * 40이고 맵은 40 * 30 크기의 2차원 배열로 선언되어 있다고 가정해 보자.

이를 800 * 600 크기의 화면에 출력하기 위한 범위 값은 다음과 같이 계산된다.

```cpp
Min X : (900 / 40) - (800 / 2 / 40) = 12

Max X : (900 / 40) - (800 / 2 / 40) = 32

Min Y : (700 / 40) + (600 / 2 / 40) = 10

Max Y : (700 / 40) + (600 / 2 / 40)  = 24
```
가로축 x의 경우 맵 배열의 12번 인덱스(배열은 0부터 시작하므로 13번째 원소) 부터 32번 인덱스 까지가 출력 범위가 되며, 세로축 y의 경우 10번 인덱스 부터 24번 인덱스 까지가 실제 출력 범위로 지정 되는 것이다.

* 맵 유효 출력 범위:  
![map_valid_output_range](/assets/img/projects/legend_of_uliyana/calcula_map1.png)  
![map_valid_output_range](/assets/img/projects/legend_of_uliyana/calcula_map0.png)

출력 범위가 정해졌으면 이제 이 칸 번호에 타일 이미지의 너비와 높이를 각각 곱하여 캐릭터의 좌표와 같은 단위로 만들어야 한다.

이것을 플레이어 캐릭터의 좌표 범위와 같은 단위로 환산하면 x축의 범위는 480 <= x <= 1280, y축의 범위는 400 <= y <= 960이 된다.

하지만 이 값들을 화면에 출력 하면 출력 화면의 범위를 넘어서게 되므로 출력 유효 범위의 좌표 값(칸 번호 값이 아니다)에 offset 값을 더하면 실제 출력 좌표가 된다.

### 최종 출력
---
캐릭터의 화면내 중앙 배치를 위한 offset 값과 맵의 유효 출력 범위를 모두 구하고 난 다음에는 이 값들을 이용해 맵 이미지를 화면에 뿌린다.

맵의 인덱스 값을 가져와 여기에 타일의 크기를 곱한 뒤에 offset 값을 더하면 해당 타일이 출력 되어야 하는 실제 화면상의 좌표 값이 만들어진다.

```
출력 좌표 = (칸 번호 * 타일의 크기) + offset; //offset을 구할때 화면 중앙 좌표 중심으로 구해 offset의 부호가 +라면 여기에 - offset을 해준다.
```
* 최종 출력 화면의 좌표:  
![map_valid_output_range](/assets/img/projects/legend_of_uliyana/finaloutput0.png)  
![map_valid_output_range](/assets/img/projects/legend_of_uliyana/finaloutput2.png)

이후 다음과 같이 출력 범위 값을 가지고 그 범위 내에서 루프를 돌면서 맵 타일 배열들을 변환된 좌표 상에 순차적으로 출력한다.

```
for(행 번호 = Min_Y; 행 번호 <= Max_Y; 행 번호++)
{
   출력 좌표y = (행 번호 * 타일의 높이) + offset

    for(열 번호 = Min_X; 열 번호 <= Max_X; 열 번호++)
    {
       출력 좌표 x = (열 번호 * 타일의 너비) + offset

       출력(출력 좌표x, 출력 좌표y, 타일 이미지[타일맵[행 번호][열 번호]])
    }
}
```

### 몬스터 AI 구현
---

몬스터들의 AI 처리는 몬스터들 모두를 배열로 묶어 순차적으로 하나씩 순회하며 처리 하는 방식으로 진행 되며, 유한 상태 기계(FSM : Finite State Machine) 모델을 기반으로 하는 상태 기반 AI 방식으로 구현 되었다.

#### 상태 기반 AI
---
상태 기반 AI는 어떤 특정 사건이 발생하면 발생한 사건의 종류에 따라 자신의 상태를 다른 상태로 전이하면서 각 상태에 맞게 정해져 있는 행동들을 수행하는 방식의 AI다.

각 상태간 전이를 보다 쉽게 파악하고 다룰 수 있게끔 유한 상태 기계 모델을 기반으로 한다.

그렇기 때문에 AI는 "정찰", "복귀", "추적", "공격" 이라는 4종류의 상태와 "정찰 범위를 벗어난 경우", "시작 위치에 도착한 경우", "적을 발견한 경우", "적을 놓쳐 버린 경우", "적이 바로 옆에 붙어 있는 경우", "적이 바로 옆에 붙어 있지 않는 경우" 라는 7종류의 사건으로 구성 되어 있다.

[4가지 상태]

| 상태 | 설명 |
| :--- | :--- |
|Roaming|최초 배치 좌표를 중심으로 일정 범위 안에서 정찰중인 상태|
|Returning|최초 배치 좌표로 복귀중인 상태|
|Tracking|플레이어를 추적중인 상태|
|Attacking|플레이어를 공격중인 상태|

<br>  

[7가지 사건]  

| 사건 | 설명 |
| :--- | :--- |
|Out Of Range|최로밍 범위(최초 배치 좌표로 부터의 거리)를 벗어남|
|Arrival|최초 배치 좌표에 도착함|
|Found Enemy|추적 범위에 들어온 플레이어를 발견함|
|Missed Enemy|플레이어가 추적 범위를 벗어남|
|Killed Enemy|플레이어가 사망함|
|Adjacent|플레이어가 바로 옆에 있음|
|Nonadjacent|플레이어와 약간 거리가 벌어짐|

AI는 7가지 종류의 사건 발생 유무에 따라 4가지 상태를 오가면서 행동하도록 설계 되었는데 이를 도식화 하면 다음과 같다.

* FSM : AI의 상태 변화:  
![map_valid_output_range](/assets/img/projects/legend_of_uliyana/LOU_AI_FSM.png)


이러한 상태 그래프는 실제 코드상에서 2차원 배열을 통해서 하나의 상태 전이표로 구현 된다.

#### 상태 전이표 참조 방법
---
AI는 2차원 배열(이하 상태 전이표)를 참조하여 자신의 상태를 변경 한다.
  
<table><tbody>
<tr><td>

</td>
<td>

**Out of**

**Range**

</td>
<td>

**Arrival**

</td>
<td>

**Found**

**Enemy**

</td>
<td>

**Missed**

**Enemy**

</td>
<td>

**Killed**

**Enemy**

</td>
<td>

**Adjacent**

</td>
<td>

**Non**

**Adjacent**

</td>
</tr>
<tr><td>

**Roaming**

</td>
<td>

Returning

</td>
<td>

</td>
<td>

Tracking

</td>
<td>

</td>
<td>

</td>
<td>

Attacking

</td>
<td>

</td>
</tr>
<tr><td>

**Returning**

</td>
<td>

</td>
<td>

Roaming

</td>
<td>

Tracking

</td>
<td>

</td>
<td>

</td>
<td>

Attacking

</td>
<td>

</td>
</tr>
<tr><td>

**Tracking**

</td>
<td>

</td>
<td>

</td>
<td>

</td>
<td>

Returning

</td>
<td>

Returning

</td>
<td>

Attacking

</td>
<td>

</td>
</tr>
<tr><td>

**Attacking**

</td>
<td>

</td>
<td>

</td>
<td>

</td>
<td>

</td>
<td>

Returning

</td>
<td>

</td>
<td>

Tracking

</td>
</tr>
</tbody>
</table>


`몬스터의 현재 상태 = 상태 전이 테이블[몬스터의 현재 상태][발생한 사건]` 과 같이 현재 상태를 테이블의 행 번호로, 사건을 테이블의 열 번호로 삼아 해당 위치에 있는 상태 값을 얻어낸 다음 이 값으로 AI의 현재 상태를 갱신하는 것이다.

예를 들어, 현재 몬스터의 AI 상태가 Attacking 상태인데 적이 죽었을 경우(Killed Enemy 사건 발생) 이 AI의 상태는 몬스터의 처음 시작 위치로 복귀하는 Returning 상태가 된다.

만약 현재 몬스터의 AI 상태가 Tracking 상태인데 적이 추적 범위를 넘어 섰을 경우(Missed Enemy 사건 발생) 몬스터는 추적을 포기하고 처음 시작 위치로 복귀하는 Returning 상태가 된다.

만약 현재 몬스터a가 플레이어A를 공격하고 있는 Attacking 상태에 있었는데 플레이어A가 한 칸 옆으로 이동을 해버린 경우, Nonadjacent 사건이 발생하며 몬스터의 AI 상태는  Attacking 상태에서 Tracking 상태로 변경되어 플레이어에 대한 추적을 시작 한다.

이어서 몬스터a가 다시 플레이어A 옆에 붙으면 Adjacent 사건이 발생하여 Tracking 상태에서 Attacking 상태가 된다.

전이표 내에 빈 공간들은 어차피 그에 해당하는 사건이 발생 하지 않는 곳이기 때문에 비어 있는 것이지만 실제로 코드상에서는 프로그램의 안전성을 위해 현재 상태와 동일한 상태로 초기화 한다.

다음은 이러한 상태 전이표를 초기화 하는 코드다.

```cpp
//AI의 4가지 행동 상태와 7가지 이벤트
enum AIState{ROAMING, RETURNING, TRACKING, ATTACKING}; //시작점으로의 정찰, 복귀, 추적, 공격
enum AIEvent{OUTOFRANGE, ARRIVAL, FOUNDENEMY, MISSEDENEMY, KILLEDENEMY, ADJACENT, NONADJACENT};//정찰 범위 넘어섬, 시작 위치 도착, 적 발견, 적 놓침, 적사망

//AI 유한 상태 기계 초기화
int nState;//상태 초기화 과정에서 쓰이는 인덱스 값
int nEvent;//이벤트 초기화 과정에서 쓰이는 인덱스 값

//상태 초기화
for(nState = 0; nState < 4; nState++)
{
 for(nEvent = 0; nEvent < 7; nEvent++)
 {
  m_EnemyAI[nState][nEvent] = (AIState)nState;//프로그램의 안전성을 위해 비어 있는 곳 전부를 현재 상태와 동일한 기본 상태로 초기화 한다. 실제 쓰이는 데이터 값들은 바로 이 다음에 덮어 씌우기 방식으로 할당 된다.
 }
}

//이벤트(그래프 상에서의 호와 같은 의미) 초기화
m_EnemyAI[ROAMING][OUTOFRANGE] = RETURNING;//로밍 중 로밍 범위(시작 점으로 부터의 일정 거리)를 이탈한 경우 복귀 상태로
m_EnemyAI[ROAMING][FOUNDENEMY] = TRACKING;//로밍 중 적을 발견한 경우 추적 상태로
//m_EnemyAI[ROAMING][ADJACENT] = ATTACKING;//로밍 중에 적이 인접한 경우 바로 공격 상태로, 이 경우는 없다고 봐도 무방하다.
m_EnemyAI[RETURNING][ARRIVAL] = ROAMING;//시작 지점으로 복귀 완료 했으면 다시 로밍 상태로
m_EnemyAI[RETURNING][FOUNDENEMY] = TRACKING;//복귀 중 적을 발견한 경우 추적 상태로
//m_EnemyAI[RETURNING][ADJACENT] = ATTACKING;//복귀 중 적이 인접한 경우 바로 공격 상태로, 이 경우 또한 위 Romaing 중의 Adjacent 사건 발생 만큼 발생 빈도가 낮다.

m_EnemyAI[TRACKING][MISSEDENEMY] = RETURNING;//추적 중 적이 추적 범위를 벗어나면 복귀 상태로
m_EnemyAI[TRACKING][KILLEDENEMY] = RETURNING;//추적 중 적이 죽으면 복귀 상태로
m_EnemyAI[TRACKING][ADJACENT] = ATTACKING;//추적 중 적이 인접칸에 있으면 공격 상태로, 발생 빈도가 매우 높다.
m_EnemyAI[ATTACKING][NONADJACENT] = TRACKING;//공격 중 적이 인접칸을 벗어나면 다시 추적 상태로
m_EnemyAI[ATTACKING][KILLEDENEMY] = RETURNING;//공격 중 적이 죽으면 복귀 상태로
```

<br>
이렇게 초기화된 상태 전이 테이블은 다음과 같이 특정 조건 하에서 몬스터의 AI상태를 바꾸기 위해 참조 된다.

```cpp
if(fDistance <= 5.0f)//만약 플레이어가 몬스터로 부터 5칸 이내의 거리에 위치한 경우
{
 ProcessAIEvent(gEnemy_Array[i], SEEKENEMY);//적(플레이어) 발견 이벤트 알림

 //ProcessAIEvent 함수는 p_enemy->SetAIState(m_EnemyAI[p_enemy->GetAIState()][p_event]); 명령어를 Wrapping 한 함수다.
}
else if( fDistance > 6.0f && gEnemy_Array[i]->GetPersonType() != FINAL_BOSS_NUM)//플레이어가 일반몹과 준보스몹의 추적 거리 7을 벗어난 경우
{
 ProcessAIEvent(gEnemy_Array[i], MISSEDENEMY);//일반몹과 준보스몹 상태기계에 사거리 벗어남 이벤트를 알림
}
```
<br>
이것으로 몬스터는 다음과 같이 변화된 AI의 상태에 따라 그에 맞게 설정된 행동들을 수행할 수 있게 된다.

```
if(몬스터의 현재 AI 상태 == 공격중)
  공격 속행();
```

TRACKING과 RETURNING 상태의 경우 각각 주어진 목표 좌표 까지 길 찾기를 수행 하는데 이를 위해 A*(A Star) 알고리즘이 사용 되었다.

#### 길 찾기 AI
---
몬스터의 길 찾기 AI 구현을 위해 A*(A Star) 알고리즘을 사용 하였으며 타일맵 기반으로 작동 한다.

A* 알고리즘은 노드 레벨 단위로 점진적으로 탐색을 진행해 나가는 그래프의 BFS(Breadth First Search) 탐색 알고리즘과 대략적인 방향을 추정한 뒤에 해당 방향으로 탐색을 진행을 하다가 중간에 결과가 좋지 않은 경우 탐색을 중지하고 마지막으로 추정 결과가 좋았던 위치로 되돌아오는 발견적(Heuristic) 알고리즘 기법들을 혼용하는 방식으로 구현 된다.


이를 위해 각각의 맵 타일들은 다음과 같은 자료구조를 가지며 이를 토대로 A* 알고리즘이 작동 된다.

```cpp
bool m_marked;//현재 칸의 탐색 완료 유무
float m_distance;//길찾기 시작 칸에서 부터 현재 칸 까지의 누적 거리
int m_lastx;//현재 칸으로 부터 가장 가까운 거리에 있는 이전 칸의 좌표(칸 단위)
int m_lasty;
```

다음은 게임내 사용한 약간 변형된 A* 알고리즘의 의사 코드와 그 순서도다.

```
A_Star_Algorithm(map, start, goal)

Priority Queue q
q.Enqueue(start)

while(q != EMPTY)
  current cell = q.Front;
  q.Dequeue

 if(current cell이 Mark 되어 있지 않으면)
   Mark(current)

   if(current cell이 goal 이면)
     break;
   end if

   for(8방향의 인접칸들 순회)
      if(해당 인접칸이 이동 가능한 칸이다 || Mark 되지 않았다)
         current cell에서 부터 해당 인접 칸 까지의 distance를 계산

         if(현재 인접칸의 distance 변수가 앞서 기록된 적이 있으면)
            위에서 계산한 distance 값과 adjacent cell.distance 값을 비교후 더  작은 값으로 갱신
             adjacent cell.previous = current cell
             q.Enqueue()
         end if

         else
             adjacent cell.distance = distance
             adjacent cell.previous = current cell
             q.Enqueue()
         end if
      end if
    end for
  end if
end while
end Function
```

![A-star 플로 차트](/assets/img/projects/legend_of_uliyana/astar_flowchart.png)

현재 AI의 위치를 길찾기의 시작점으로 놓고 이를 Queue에 집어 넣는다.

그리고 Queue에 들어온 칸을 현재 칸으로 삼고 해당 칸에 데이터를 처리했다는 표식을 해둔다.

여기서 주의해야 할 부분은 현재 칸으로 체택된 칸에만 처리 표식을 해둔다는 것이다.

그리고 현재 칸으로 부터 동,서,남,북 4방위에 위치해 있는 인접칸들을 순회하면서 현재칸에서 부터 인접칸 까지의 누적거리를 해당 칸에 기록해 둔다.

그리고 이어서 누적거리와 인접칸에서부터 목표칸 까지의 직선 거리를 합산 하여 이를 발견적 값으로 삼은 뒤에 이 칸을 Queue에 집어 넣는다.

이때 누적거리는 그 경로상의 타일 배치가 직선인 타일의 경우 1, 대각선인 경우 그 빗변 길이인 √2로 상정하여 시작점 부터 그 값을 모두 누적하여 계산한다.

발견적 값이 작을 수록 보다 목표 지점과 가까운 경로의 칸을 의미하는 것이며 Priority Queue의 특성으로 인해 Queue 안에서 그만큼 높은 우선 순위를 갖게 되므로 다른 칸들 보다 먼저 탐색 대상에 오르게 된다.

![A-star 알고리즘 그리드](/assets/img/projects/legend_of_uliyana/AStar_Grid.png)

예를 들어, A칸과 B칸이 있을때  A칸의 경우 누적거리는 시작점 S칸으로 부터 A칸 까지의 거리인 1.41이 되며 발견적 값은 누적 거리 1.41과 A칸으로 부터 목표 칸인 E칸 까지의 직선 거리인 5의 합인 6.41이 된다.

B칸의 경우에는 누적거리 1.41, 발견적 값이 5.01이 된다.

그리고 이 두 칸이 Queue에 들어갔을때 발견적 값이 1.4 더 낮은 B칸이 A칸 보다 먼저 검색 대상으로 선택 되어 지게 되는 것이다.

이후 해당 인접칸은 현재칸으로 상정 되어 처리 표식을 받기 전까지 여러 차례 인접칸으로 선택 되어 Queue에 넣어 지게 되고 이 과정에서 이전에 Queue에 넣어졌을때의 누적 거리 값 보다 현재의 값이 더 적은 경우 현재칸으로 상정된 칸을 가리키도록 설정 된다.

이 과정이 반복 되다보면  각각의 맵 타일 안에는 보다 가까운 경로의 칸을 가리키는 값들이 저장되어 있게 되고 이들을 하나로 이으면 결국에는 목표점에서 부터 시작점으로 향하는 일련의 경로가 만들어지게 된다.

이것이 바로 목표 까지의 최단 경로이다.

AI는 만들어진 이 최단 경로를 목표점에서 부터 시작점 바로 앞칸 까지 되짚어 오면서 이들을 스택에 쌓아 두었다가 이를 다시 차례 차례 pop 하여 참조 하는 방식으로 이동 한다.

이러한 작업들은 몬스터가 TRACKING, RETURNING 상태에서 좌표를 한 칸씩 이동 해야 할 때마다 이루어지는데 덕분에 이동을 위해 필요한 경로상의 한 칸의 값을 빼고는 모두 버려도 상관 없기 때문에 A* 알고리즘으로 만들어진 최단 경로를 저장할 스택을 따로 쓰지는 않았다.

다음은 A* 알고리즘으로 경로를 맵 타일에 저장한 다음 이를 활용하기 위해 목표점 부터 시작점 까지 경로를 되짚는 과정의 코드이다.

```
AStar(p_one, p_two);

int lx, ly; //현재 칸이 가리키고 있는 이전 칸의 좌표
int x, y;

lx = x = p_two->GetX();
ly = y = p_two->GetY();

while(x != p_one->GetX() || y != p_one->GetY())//목표 좌표에서 현재 캐릭터의 바로 이전 좌표 까지 되짚는다.
{
 lx = x;
 ly = y;
 x = mTilemap.Get(lx, ly).m_lastx;
 y = mTilemap.Get(lx, ly).m_lasty;

 if(x == -1 || y == -1)//-1값은 경로가 없다는 의미이다(ex : 꽉 막힌 방안).
  return rand()%4;
}

//루프를 마치면 x, y에는 AI의 현재 칸이, lx, ly에는 현재칸으로 부터 최단 경로 상의 첫번째 칸의 좌표 값이 들어 있게 된다.

if(ly < y)
 return NORTH;//북쪽으로 한 칸 이동

if(lx > x)
 return EAST;//동쪽으로 한 칸 이동

if(ly > y)
 return SOUTH;//남쪽으로 한 칸 이동

if(lx < x)
 return WEST;//서쪽으로 한 칸 이동
```

마지막으로, 위와 같은 로직은 몬스터들이 배열로 묶여 순차적으로 한 번에 처리되는 만큼 연산의 부담이 적지 않은 구조다.

특히나 AI 마다 A* 알고리즘을 이용해 경로를 만들어 맵에 저장하고 이를 되짚어 가며 사용한 뒤에 다시 다른 AI의 길찾기 처리 순서가 되면 이전 AI가 기록하고 사용한 맵의 정보들을 전체 맵 단위로 모두 초기화 하기 때문에 더욱 그렇다.

이에 더하여 AI 별로 각기 다른 길찾기 알고리즘들을 사용 하도록 하는 식으로 몬스터 별로 특성을 심어 주기에도 어려운 구조다.

이를 해결 하기 위해서는 위와 같이 맵이 A* 알고리즘을 사용하는 주체가 되어 최단 경로를 맵에 저장했다가 지웠다 하는 방식이 아닌, AI들 스스로가 A* 알고리즘을 사용하는 주체가 되도록 하여 그 최단 경로를 자신들 내부에 기록하고 이를 활용 할 수 있도록 하는 방식으로 구현해야 할 것이다.