---
title: Map Editor
description: Windows 환경에서 C++로 구현된 맵 및 이벤트 에디터
author: hyeonsu-choe
date: 2013-11-30 14:49:00 +0900
last_modified_at: 2025-10-26 22:32 +0900
categories: [Projects, Windows]
tags: [personal, win32, cpp, tool, editor, game, rpg]
toc: true
pin: false
render_with_liquid: false
image:
  path: /assets/img/projects/legend_of_uliyana_map_editor/program_using.png
  # lqip: 
  alt: Editing screenshot of Legend Of Uliyana Map Editor
---

| 구분 | 내용 |
|:----|:----|
|플랫폼|Windows|
|개발 도구 및 사용 언어|MS Visual Studio 2010, C++, Win32 API|
|개발 기간| 2013년 7월 ~ 2013년 11월|
|저장소| [바로 가기](https://github.com/hyeonsu-choe/uliyana-suite)|

## 1. 프로그램 소개

개인 사이드 프로젝트인 싱글 롤플레잉 게임 **Legend of Uliyana**의 맵 제작을 위해 만든 툴.

처음 부터 계획적으로 제작한 프로그램은 아니며 게임 제작 중간에 작업 편의를 위해 개발 하였다. 

맵 타일 및 플레이어의 시작 위치, 몬스터, 아이템, 간단한 스위치 이벤트, 다른 맵으로의 이동 지점 등의 오브젝트들을 배치 할 수 있다.  

기본적으로 새로운 맵 파일을 새로 생성하거나 기존 파일들을 읽어 들여 편집 할 수 있는 기능들을 제공하며, 사용상의 편의를 위해 맵의 축소 및 확대, 페인트, 전체 삭제 기능들을 제공 한다.  

![프로그램 사용 화면](/assets/img/projects/legend_of_uliyana_map_editor/program_using.png)


## 2. 프로그램 구조

프로그램의 모든 이미지는 **더블 버퍼링 기법**을 통해 다루어진다.  

더블 버퍼링 기법이란 메모리 버퍼와 출력 버퍼를 2개 두고서 우선 메모리 버퍼에 필요한 이미지들을 그린 후에 이 작업이 끝나면 이를 출력 버퍼로 옮겨 최종적으로 모니터에 출력하는 방법이다.  

이를 통해 하나의 버퍼만을 이용해 출력 작업을 하는 과정에서 해당 버퍼에 이미지 리소스들이 빠르게 그려지고 지워지고를 반복하면서 발생하는 점멸 현상을 방지해 이미지들이 보다 부드럽게 화면에 보여지도록 한다.  


### 클래스 구조
---

프로그램은 크게 3개의 클래스 모듈로 구성되어 있다.

| 모듈 | 설명 |
| :--- | :--- |
|cMapEditor|cPalette와 cScreen의 인스턴스들을 관리 및 제어|
|cPalette|cScreen에서 사용할 맵 타일 이미지와 스크롤 관련 정보들을 제공|
|cScreen|cPalette로부터 제공 받은 정보들을 토대로 각종 오브젝트 이미지들을 화면에 출력|

참고로 클래스 `cScreen`과 `cPalette` 모두 `cMapTool` 에서 파생된 클래스이다.  

#### 1) class cPalette
화면에 뿌려져야 할 각종 맵 타일 이미지와 선택된 타일 번호, 해당 이미지 파일의 이름 등의 많은 정보들을 갖고 있다.  
사용자가 화면상의 팔레트 윈도우에서 하나의 타일 이미지를 선택 했을 때, cPalette는 해당 오브젝트가 제대로 마우스 클릭이 되었는지 감지한 후에 이와 관련된 정보들을 cScreen 내의 cPaletteInfor로 전송 한다.  

#### 2) class cScreen
클래스 cPaletteInfor를 포함하고 있으며 이는 cPalette로부터 여러 정보들을 받는데 사용된다.  
이후 cPalette로부터 얻은 정보들을 토대로 하여 해당 이미지들을 화면에 뿌린다.  
화면 확대, 축소, 리사이징 기능을 수행하는 함수들을 가지고 있기도 하다.  

#### 3) class cMapEditor
일종의 컨테이너이지만 cPalette와 cScreen 상호간 정보 교환이 원활 할 수 있도록 매개 역할을 수행한다.  
예를 들어, 만약 팔레트가 변경 되면 MapViewer를 업데이트 시킨다.  

다음은 각 모듈들이 서로 어떠한 흐름을 통해 동작하는지 보여주는 그림이다.  

![각 모듈 간 처리 순서 및 흐름](/assets/img/projects/legend_of_uliyana_map_editor/object_flow.png)

이는 "cPalette" 와 "cScreen"이 기능적으로 "cMapEditor"의 부분 집합임을 의미하는 것이기도 하다.  


### 주요 기능 소개
---

#### 1) 전체 맵 크기의 조절  

```cpp
Array<int> MapData(width, height, depth);
```

맵 데이터는 3차원 배열로 다루어지며 이때 배열은 C++의 템플릿 형식으로 작성된 배열이다.  

너비와 높이는 각각 배열상의 좌표 X와 좌표 Y의 최대 값이며 깊이는 맵을 구성하는 요소들의 배치상 편의를 위해 구분한 층수를 의미한다.  

구조적으로는 유저에 의해 맵 크기 조절이 요구 되었을 때 객체 "MapEditor"가 "MapViewer"로 하여금 맵 크기를 조절하도록 지시하는 방식으로 동작 한다.  


#### 2) 맵의 확대와 축소  

![맵 스케일](/assets/img/projects/legend_of_uliyana_map_editor/map_editor_scale.png)

이 프로그램은 모든 이미지 파일들을 윈도우즈 API의 비트맵 형식으로 다룬다.  

만약 너비 100, 높이 200, 깊이 4의 사이즈 정보가 주어지면 맵을 구성하는 타일의 총 개수는 100 * 200 * 4로 80000개가 된다.  

여기에 더하여 4배에서 1/4배에 이르는 맵의 확대와 축소 범위를 그래픽 상에 표현하기 위해서 보다 충분한 타일 개수와 이에 상응하는 크기의 메모리를 확보하는 방식으로 동작하고 있는데 1/4의 크기의 경우에는 기존 버퍼로도 충분히 표현 할 수 있기 때문에 최대 범위 값인 4배를 기준으로 삼아 맵의 메모리 크기를 확보 한다.  

결국 위의 예의 경우 맵의 확대와 축소를 위한 전체 타일 개수는 총합 80000개가 아닌 여기에 최대 배수 값인 4를 곱한 320000개가 되는 것이다.  

따라서 타일의 개수는 프로그램 내부에서의 메모리 공간 개수와 일치하게 된다.  


#### 3) 페인팅 기능  

페인팅 기능은 페인트 함수의 재귀적 호출 구조를 통해 해당 페인트 작업 대상 범위를 선택된 이미지 타일로 하나씩 바꿔 나가는 방식으로 구현 되어 있다.  

다음은 이에 대한 개략적인 알고리즘이다.  

```
BEGIN DrawPaint : 매개 변수1 : 좌표 x , 매개 변수2 : 좌표 y

재귀 종료 조건 1 : 좌표 x 또는 좌표 y 값이 맵의 좌표 범위를 초과 하였을 경우
(맵의 유효 범위 내에서 페인트 작업을 수행하여 재귀적 호출에 의한 스택 오버 플로우 발생 문제를 막고 전체적인 프로그램의 안전성을 보장).

재귀 종료 조건 2 : 페인트 도구로 선택된 타일 값과 현재 페인팅 작업 수행 대상이 되는 좌표상의 타일이 서로 동일한 경우
(이는 하나의 타일에 페인트 작업이 여러번 중복 수행 되어지는 것을 방지).

재귀 종료 조건 3 : 페인팅 작업의 유효 범위 즉 교체 대상으로 지정된 타일과 현재 페인팅 작업 수행 대상이 되는 좌표상의 타일이 서로 다른 경우
(페인팅 작업 수행이 작업 범위의 경계(끝)에 도달 하였음을 알림).

call DrawPaint(x    , y - 1);
call DrawPaint(x + 1, y);
call DrawPaint(x    , y + 1);
call DrawPaint(x - 1, y);

END DrawPaint
```

### 맵 파일의 메모리 구조

![메모리 구조](/assets/img/projects/legend_of_uliyana_map_editor/memory_structure.png)

맵 데이터는 위와 같은 자료 구조로 구성 되어져 있다.  

마지막으로 이 맵 에디터 툴은 *"Ron Penton"*이 쓴 *"게임 프로그래머를 위한 자료구조와 알고리즘"*이라는 책의 SDL 라이브러리를 통해 작성된 예제를 참조하여 **WIN32 API 기반으로 만들었음**을 밝힌다.
