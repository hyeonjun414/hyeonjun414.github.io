---
title: "Game : 렌더링"
excerpt: "여러 용어와 원리"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Game
tags:
  - [게임 그래픽, rendering]
date: 2021-04-13
breadcrumb: true
---




# 게임 그래픽 렌더링 원리와 최적화 요소

참고 블로그 : C언어 예술가 [https://thrillfighter.tistory.com/179](https://thrillfighter.tistory.com/179)

## 사용 용어들

- 버텍스(vertex) : 3차원 공간에서 위치를 표시하는 점. x,y,z좌표 이외에 색상 방향 텍스쳐 좌표의 정보를 가짐. 3D 오브젝트는 버텍스를 이용하여 만들고, 렌더링 시에도 버텍스 정보가 있어야 처리가 가능하다.
- 픽셀(Pixel) : 2차원 평면을 구성하는 점.
- 투영(Projection) : 물체의 그림자를 다른 물체 위에 비추는 일 또는 비친 그림자. (입체가 아닌 평면으로 나타남)
- 버퍼(Buffer) : 메모리 공간
- 시야 절두체(FV)(View Frustum) : 카메라 화면에 보이지 않는 영역으로 공간의 형상이 피라미드의 꼭지점을 잘라낸 형태

## 시야각(FOV)

Direct3D에서는 상하 방향의 각을 시야각으로 사용

Aspect : 화면의 가로 길이와 세로 길이의 비율(Aspect = 가로길이 / 세로길이)

## 알파블렌딩(Aplha Blending)

앞쪽에 있는 물체가 투명도가 있을 경우 뒤의 물체들이 함께 보이도록 처리하는 기법

투명도가 있는 유리와 그 뒤에 공이 있다면 유리의 색상과 공의 색상을 적당히 혼합에 그 결과값을 최종 픽셀 색상으로 결정한다.

새로 만들어진 픽셀 값 : Src, 이미 저장되어 있는 기존의 픽셀 값 : Dst

최종 픽셀의 값 = (Src * Src Aplha) + (Dst * Dst Alpha)

알파블렌딩 처리를 해야 할 물체는 항상 제일 마지막에 그려야한다. 순서가 꼬여 색상 처리가 제대로 되지 않기 때문.

작성중..........