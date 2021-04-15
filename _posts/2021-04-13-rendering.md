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

## 깊이버퍼(z-버퍼)

버텍스 셰이더 단계에서 3차원 공간의 값을 2차운 평면 상의 값으로 바꿀 때 카메라와 해당 점의 거리 값을 저장해 놓는 버퍼

깊이버퍼를 구성하는 픽셀의 개수 = 화면을 구성하는 픽셀의 개수

화면의 모든 픽셀에 대해서 깊이 정보를 저장하게 됨

## 스텐실 버퍼, 스텐실 테스트

깊이버퍼와 함께 만들어 지는 버퍼, 화면의 모든 픽셀에 대해 부가적인 정보 저장

## 2차원 화면이 그려지는 원리

2차원 데이터 = 이미지 데이터

복사하는 방식 - 2D게임의 경우 표현되는 모든 이미지는 미리 그려져 있어야 한다.

## 3차원 화면이 그려지는 원리(공간압축)

유리를 통해서 보이는 공간을 압축하여 유리벽에 붙도록 만드는 원리

자세한 내용은 그래픽스를 공부해야한다.

## 렌더링 파이프 라인

공간을 구성하는 데이터 → 데이터를 공간압축 또는 투영 → 평면상의 값으로 변경 → 화면 출력

### 세부과정

1. 공간을 구성하는 데이터 준비
2. 3차원 공간의 점을 고오간 압축 이후의 유리 평면상의 위치 값으로 변환
3. 픽셀 채우기
4. 텍스쳐 적용하기 : 색상데이터 넣음
5. 픽셀 테스트
6. 버퍼에 출력 : 렌더링 파이프 라인 끝에서는 픽셀이 하나씩 나오고 만들어지자마자 출력을 한다. 이떄 티어링이 발생하는데 픽셀을 한 곳에 모아서 다 처리되면 한번에 화면에 보여주는 방식으로 처리하는 것이 백버퍼와 프레임 버퍼이다.

## 텍스쳐

- 화면에 그려질 픽셀의 색상 값을 미리 만들어 저장해 놓은 이미지 데이터
- 텍스처를 구성하는 픽셀 = 텍셀(Texel)
- (u,v) : 전체 길이에 대한 비율 값
- (u,v) 형식으로 특정 텍셀의 위치 표시, 텍스처 좌표를 이용해 텍셀값을 읽어오고 샘플러라는 하드웨어 장치에 의해 빠르게 처리된다.
- 텍스처 이미지를 만들 때는 여러장으로 나누어 만드는 것보다 한 장으로 합쳐 만드는 것이 더 효율적

## 셰이더

- 그래픽 카드 내부에 이미 만들어진 동작방식에 대한 명령
- 셰이더의 구성
    1. 버텍스 셰이더 : 공간 데이터 정보를 평면 상태 정보로 만드는 기능(공간 압축)
    2. 지오메트리 셰이더 : 삼각형의 세 점 내부의 픽셀을 추가하는 기능
    3. 픽셀 셰이더 : 픽셀에 텍스처를 입히는 기능
- 셰이더 언어 : Cg, HLSL, GLSL

## 픽셀의 색 결정 요인

### 버텍스 컬러

- 그래픽 툴에서 데이터를 제작할 때 버텍스 정보에 추가할 수 있는 요소
- 위치 값, 방향 벡터, 버텍스 컬러, 텍스처 좌표 등.
- 버텍스 컬러 값을 넣어주지 않으면 기본 흰색 사용

### 라이트 컬러

- 공간을 설정해 준 빛의 색상과 방향에 따라 픽셀 색상에 영향
- 버텍스 컬러 값과 라이트 컬러 값이 동시에 작용하면 두 색상을 곱해 처리
- 라이트 컬러 적용할 때 명암 계산을 함께 진행

### 텍스처

- 텍스처에서 텍셀 값을 가져와 지정된 픽셀에 적용
- 라이트가 함계 적용된다면 라이트 색상과 텍셀의 컬러 값이 곱해짐

### 알파블랜딩과 안개효과

- 픽셀 셰이더 단계를 지나면 알파블렌딩 단계에서 이미 출력되어 있는 픽셀의 값과 새로 넘겨받은 픽셀의 값을 어떻게 혼합할 것인가를 결정
- 안개효과 : 안개의 농도와 색상에 픽셀의 최종 색상이 곱해짐

## 장면 처리 속도 향상을 위한 것들

필요한 계산을 하고 리소스를 준비하고 그리기 시작하여 렌더링 파이프라인이 작동하기 시작하면 상당히 긴 시간이 소요된다. 그리고 그 시간이 지나야 데이터가 처리되 화면에 그려지게 된다.

해결방법 : 렌더링 함수 호출 횟수를 줄인다. 화면에 보이는 물체만 그리도록 미리 검사를 해서 처리, FOV 안에 들어있지만 다른 물체에 의해 완전히 가려지는 물체는 그릴 필요 없음. 데이텉 제작이 객체를 병합하여 합친다(때로는 분리)