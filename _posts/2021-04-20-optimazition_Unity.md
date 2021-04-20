---
title: "Game : 각종 최적화 기법"
excerpt: "여러 최적화 방법"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Game
tags:
  - [Unity, Design, 최적화]
date: 2021-04-20
breadcrumb: true
---


# 유니티 : 각종 최적화 기법

## 오브젝트 풀링(Object Pooling)

짧게 사용되고 사라진 뒤에 다시 사용되는 오브젝트의 경우 매 번 다시 생성/해제를 반복하면 메모리 부하가 심해진다.

그래서 화면에만 보이지 않게만 하고 해제시키지 않은 채로 필요할때 가져와 사용하는 방법을 사용하는데 그것이 오브젝트 풀링이다.

메모리 할당을 더 간단하게 할 수 있고 동적 메모리 할당 오버헤드와 가비지 컬렉션을 줄일 수 있다.

하지만 단점도 존재한다.

1. 다른 목적으로 사용할 가용 힙 메모리의 양이 줄어든다. 따라서 현쟂 막 생성한 풀 외에도 메모리를 계속 할당한다면, 가비지 컬렉션이 더 자주 실행된다.
2. 가비지 컬렉션에 걸리는 시간은 살아있는 오브젝트 수에 비례하여 증가하기 때문에 매번 더 느려질 수 있다.
3. 너무 큰 풀을 할당하거나 또는 풀에 있는 오브젝트가 한동안 필요가 없는 상황에서 풀을 활성화하여 유지한다면 성능에 지장이 생기게 된다.

따라서 "오브젝트 풀 오버헤드 < 오브젝트 풀을 사용하지 않았을 때 생기는 메모리 부하"인 경우에만 사용 해야 한다.

## Class vs Struct

클래스의 인스턴스는 힙 영역에, 구조체의 인스턴스는 스택에 할당된다.

즉, 구조체는 아무리 만들고 없애도 가비지 컬랙터에 부담을 주지 않지만 클래스를 반복적으로 할당 해제하는 것은 가비지 컬랙터에 부담을 주게 된다.

장시간 유지되어야하는 오브젝트는 클래스에, 단시간만 사용할 오브젝트는 구조체에 사용해주는 것이 좋다.

메소드에서 반복적으로 생성이 필요한 사용자 정의 테이블을 만들때는 구조체 형식을 사용해야할 것같다.

## Immutable, String 반복 생성

```csharp
//<리스트 1> ‘+’로 연결한 문자열 조합
class Names
{
    public string[] name = new string[100];
    public void Print()
    {
        for (int index = 0; index < name.Length; index++)
        {
            string output = "[" + index + "]" + name;
            Console.WriteLine(output);
        }
    }
}
```

C#에서 String은 Immutable 객체다. 따라서 "안녕" + "하세요" 를 연산하면 새로운 안녕하세요 객체를 생성하게 된다. 하지만 이경우 반복문 안에서 객체의 할당과 해제가 수차례 발생하게되고 메모리 단편화 문제가 발생한다.

```csharp
//<리스트 2> System.Text.StringBuilder 객체 사용
class NewNames
{
    public string[] name = new string[100];
    private StringBuilder sb = new StringBuilder();
    public void Print()
    {
        sb.Clear();     // sb.Length = 0;
        for (int index = 0; index < name.Length; index++)
        {
            sb.Append("[");
            sb.Append(index);
            sb.Append("] ");
            sb.Append(name);
            sb.AppendLine();
        }
        Console.WriteLine(sb.ToString());
    }
}
```

위와 같이 StringBuilder를 이용해 Append()메소드를 통해 문자열을 추가하며 string객체를 만들어내는 것이 아니라 이미 잡아놓은 메모리 공간에 문자열만 복사하여 한번에 ToString()으로 string객체를 생성한다.

내부의 for문