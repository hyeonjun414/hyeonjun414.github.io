---
title: "Game : 전략과 상태의 차이"
excerpt: "상태를 자동으로 체크!"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Game
tags:
  - [Unity, Design]
date: 2021-06-05
breadcrumb: true
---




# 유니티 : 전략과 상태의 차이

## 공통점

상태와 전략의 기본 형태는 비슷하다.

![/assets/images/posts/2021-06-05/stateandstrategy/Untitled.png](/assets/images/posts/2021-06-05/stateandstrategy/Untitled.png)

상태와 전략 둘다 Context에서 InterfaceParent.Execute()를 실행한다는 것이 중요한 점이다. 두 패턴 모두 객체지향적인 설계이기 때문인데, ConcreteSon의 종류가 현재는 2개 뿐이지만 수가 늘어나더라도 Context는 해당 함수만을 실행할 뿐이므로 확장에는 열리고, 수정에는 닫혀야 한다는 객체지향의 원리, OCP를 지킬 수 있다. 만약 Context에서 실제 객체의 함수를 직접호출했다면 종류가 늘어날 때마다 불필요한 코드도 늘어날 것이다.

## 차이점

전략패턴과 상태패턴의 차이는 바로 SetConcreteSon을 어디서 호출할 것인가? 이다.

두가지는 사용되는 방향이 다르다.

전략패턴은 외부에서 인위적으로 ConcreteSon 객체를 변경후 Execute()함수를 호출할때 사용하고,

상태패턴은 Client는 단순히 Context.Operate() 함수를 호출해주고, 그때마다 내부적으로 알아서 ConcreteSon 객체가 바뀌게 하고 싶을 때 사용한다.

두 패턴의 골자는 비슷하지만 목적이 다르므로 필요한 경우에 따라 사용하는 것이 좋다.

참고 : [https://glikmakesworld.tistory.com/12?category=797136](https://glikmakesworld.tistory.com/12?category=797136)