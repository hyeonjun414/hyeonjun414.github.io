---
title: "Game : 오브젝트 풀링"
excerpt: "유니티에서의 메모리 관리"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Game
tags:
  - [Unity, Design]
date: 2021-04-18
breadcrumb: true
---

# 유니티 : 오브젝트 풀링

오브젝트 풀링 기법은 유니티 게임 프로그래밍을 배울 때 먼저 배우는 기법 중 하나이다.

일단 오브젝트 풀링이 왜 필요하고 정체가 무엇인지 예시를 들어 설명하겠다.

전투기 슈팅 게임을 만들 때, 유저는 화면에 적이 있든 없든 총알 발사 버튼을 막 누르게 된다.

이 때 생성되는 총알 오브젝트는 Instantiate, Destroy 함수를 이용해서 생성과 소멸을 끊임없이 반복한다면  어떻게 될까?

## 1. 메모리 단편화 문제의 발생

바로 메모리 단편화라는 문제가 생긴다.

메모리 단편화에는 외부 메모리 단편화와 내부 메모리 단편화가 있는데, 위의 상황에서는 외부 메모리 단편화가 발생한다.

외부 메모리 단편화란, 메모리에 빈 공간이 충분함에도 불구하고 빈 공간이 떨어져있어서, 새로운 오브젝트를 만들지 못하는 현상을 말한다.

떨어진 빈 공간의 합은 오브젝트보다 크지만 공간이 나누어져 있기 때문에 들어갈 수 없는 것이다.

지속적으로 생성과 파괴를 수행하면 빈 공간이 계속 생기게되고, 메모리 관리가 되지않는다.

이런 상황을 외부 메모리 단편화라고 부른다.

## 2. 유니티는 메모리 단편화를 해결 못한다.

보통 C#에서는 가비지컬렉터가 메모리 단편화를 Compaction(압밀, 압축밀착)해서 해결한다.

Compaction이 진행되면 오브젝트들의 메모리 공간을 좌로 밀착시키고, 빈공간을 이어지게 해서 오브젝트5에게 메모리 공간을 할당해 줄 수 있다.

하지만, 유니티의 가비지컬렉터는 Compaction기능을 지원하지않는다.

그렇기 때문에 생성과 파괴를 자주하여 빈공간이 생기게 되면 객체가 들어갈 공간이 없어 메모리 점유가 늘어나게되어 비효율적인 메모리 운용이 발생한다.

그래서 Instantiate와 Destroy의 사용을 최대한 자제하는게 유니티 메모리 최적화의 핵심이다.

## 3. 기초적인 오브젝트 풀링(Object Pooling)

예와 같이 전투기 슈팅게임을 만든다고 할때, 버튼을 누를 때마다 총알이 나가는데, 이때마다 Instantiate를 호출하여 총알을 생성하고, 총알이 적에게 닿거나 맵 밖으로 나가면 Destroy로 총알을 삭제하면 메모리에 빈공간이 생길것이다.

그래서 수십발의 총알을 미리 생성하여 적에게 총알이 닿거나 맵 밖으로 총알이 나가면 SetActive(false)를 해서 안보이게 한 후, 나중에 총알을 재사용하는 방법을 오브젝트 풀링이라고 한다.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
 
//전투기 클래스
public class Flight : MonoBehaviour
{
    //총알 프리팹
    public Bullet prefab_bullet;
 
    //총알 Pool
    private List<Bullet> bulletPool = new List<Bullet>();
 
    //내가 생성할 총알 갯수
    private readonly int bulletMaxCount = 10;
 
    //현재 장전된 총알의 인덱스
    private int curBulletIndex = 0;
 
    void Start()
    {
        //총알 10개 미리 생성
        for(int i = 0; i < bulletMaxCount; ++i)
        {
            Bullet b = Instantiate<Bullet>(prefab_bullet);
            
            //총알 발사하기 전까지는 비활성화 해준다.
            b.gameObject.SetActive(false);
 
            bulletPool.Add(b);
        }
    }
 
    void Update()
    {
        FireBulet();
    }
 
    //총알 발사
    void FireBulet()
    {
        //마우스 좌클릭 할 때마다 총알 발사
        if (Input.GetMouseButtonDown(0))
        {
            //발사되어야할 순번의 총알이 이전에 발사한 후로 아직 날아가고 있는 중이라면, 발사를 못하게 한다.
            if(bulletPool[curBulletIndex].gameObject.activeSelf)
            {
                return;
            }
 
            //총알 초기 위치는 전투기랑 같게
            bulletPool[curBulletIndex].transform.position = this.transform.position;
 
            //총알 활성화 해주기
            bulletPool[curBulletIndex].gameObject.SetActive(true);
 
            //방금 9번째 총알을 발사했다면 다시 0번째 총알을 발사할 준비를 한다.
            if (curBulletIndex >= bulletMaxCount - 1)
            {
                curBulletIndex = 0;
            }
            else
            {
                curBulletIndex++;
            }
        }
    }
}
```

총알을 여러개 만들어 리스트에 담고, 그것을 필요할 때 빼서 쓰는 것이 기본 틀이다.

안쓰는 총알 리스트와 활성화된 총알 리스트를 따로 만들어, 총알을 발사하면 양쪽 리스트로 옮겨주며 관리를 해준다. 만약 게임에 총알 종류가 여러개라 바꿔쏠 수 있는 게임이라면, 리스트를 여러개 만들어 Get함수를 만들어 현재 유저가 설정한 총기 종류에 따라 다른 리스트의 총알을 리턴하는 방법도 있다.

## 마무리

오브젝트 풀링은 메모리 단편화 문제 때문에도 쓰이고, 애초에 Instantiate와 Destroy 함수 자체가 비용이 큰 함수이기 때문에 런타임때 자주 써주는건 좋지 못하다.

오브젝트 풀링의 단점이라면 내가 총알을 사용하지 않을 때에도 총알이 메모리에 상주한다는 것인데, 총알의 max 갯수는 테스트를 통해 적당한 수치로 조절해주는 것이 좋다.