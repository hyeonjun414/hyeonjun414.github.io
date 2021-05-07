---
title: "Game : 컴포지트"
excerpt: "아이템 구조"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Game
tags:
  - [Unity, Design]
date: 2021-05-07
breadcrumb: true
---


# 유니티 : 컴포지트

인벤토리 창이 존재하는 RPG게임이 있다고 할때,  아이템 슬롯에는 표션이나 무기 같은 아이템을 보관할 수 있는데, 아이템 중에는 4차원 가방이라는 아이템이 있다. 가방은 자신도 아이템이지만 내부적으로 아이템슬롯을 가지며 그곳에도 아이템을 보관할 수 있다. 이런 구조를 컴포지트 패턴을 이용해 설계해보자.

## 아이템 클래스 구조

![/assets/images/posts/2021-05-07/composite/Untitled.png](/assets/images/posts/2021-05-07/composite/Untitled.png)

여기서 Item가 컴포넌트, Leaf가 단일객체, Bag가 복합 객체가 된다.

## 코드

```csharp
//최상위 아이템 추상클래스
public abstract class Item
{
    public enum Type
    {
        Use,
        Equip,
        Own,
        Main
    }

    public string name;
    public Type type;

    //아이템이 수행할 동작을 여기에 정의하거나 실행.
    public abstract void Operate();

    //생성자
    public Item(string name, Type type)
    {
        this.name = name;
        this.type = type;
        Debug.Log("부모클래스 세팅 : " + name + " " + type.ToString());
    }
}
```

```csharp
//4차원 가방 클래스
public class ItemBag : Item
{
    private List itemList = new List();

    public ItemBag(string name, Type type = Type.Use) : base(name, type)
    {
        
    }

    public override void Operate()
    {
        OpenSlotWindow();
    }

    //가방의 인벤토리 창을 여는 멤버함수. 본 예제에서는 내부를 정의하진 않겠다.
    private void OpenSlotWindow()
    {

    }

    //가방에 아이템 넣을때 사용하는 함수
    public void Add(Item item)
    {
        if(itemList.Contains(item))
        {
            Debug.Log(" 버그 : 이미 해당 아이템이 리스트에 존재함.");
            return;
        }
        itemList.Add(item);
    }

    //가방에서 아이템 뺄때 사용하는 함수
    public void Remove(Item item)
    {
        if(!itemList.Contains(item))
        {
            Debug.Log("버그 : 해당 아이템이 리스트에 존재하지 않음.");
            return;
        }
        itemList.Remove(item);
    }
}
```

```csharp
//일반 아이템 클래스
public class ItemLeaf : Item
{
    //생성자
    public ItemLeaf(string name, Type type) : base(name, type)
    {
        
    }

    public override void Operate()
    {
        
    }
}
```

```csharp
//추가 예시. 물약을 만들어보았다.
public class Potion : ItemLeaf
{
    public Potion(string name, Type type) : base(name, type)
    {
        
    }

    //또 오버라이딩 해준다. 그럼 ItemLeaf의 Operate함수는 실행되지 않을 것이다.
    public override void Operate()
    {
        UsePotion();
    }

    //체력을 채우고, 물약 갯수를 1 감소시켜주는 기능을 함수내부에 만들어주면 될 것이다.
    private void UsePotion()
    {

    }
}
```

이처럼 할 수 있지만, 아이템 구조를 만들때 컴포지트 패턴을 사용하지 않을 수도 있다.

참고 : [https://glikmakesworld.tistory.com/8](https://glikmakesworld.tistory.com/8)