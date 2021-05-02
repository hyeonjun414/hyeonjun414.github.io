---
title: "Game : 싱글톤 패턴"
excerpt: "싱글톤을 사용하는 두가지 방법"
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - Game
tags:
  - [Unity, Design, 최적화]
date: 2021-04-23
breadcrumb: true
---


# 유니티 : 싱글톤 패턴

싱글톤 패턴은 아마 가장 많이 쓰는 디자인 패턴일 것 같다.

클래스 구조를 짜다보면 다른 클래스의 함수를 사용하는 경우가 있고, 전체 클래스들이 공유하는 전역변수가 필요할 수도 있다. 규모가 작은 게임에서는 public으로 변수를 만들고 인스펙터에서 의존관계를 만들어 줄 수 있지만, 게임이 커지면 다른 클래스를 참조하는 변수가 많아도 문제가 생긴다.

public 변수를 만드는 것 자체가 메모리를 사용하는 것이고 만약 클래스를 변경하거나 삭제할 때 일일이 다 바꿔줘야해서 시간을 낭비하는 경우도 생긴다.

클래스에서 공통으로 사용하는 전역변수와 데이터 그리고 게임 전체를 관리하는 매니저 클래스는 싱글톤으로 빼는게 도움이 된다.

싱글톤을 이용하면 임의의 클래스에서 직접 만든 싱글톤 인스턴스를 사용할 수 있다.

## 싱글톤 사용하는 방법

### 1. Monobehaviour를 상속받아 Hierarchy에 존재하게 하는 것

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameMgr : MonoBehaviour
{
    //게임매니저의 인스턴스를 담는 전역변수(static 변수이지만 이해하기 쉽게 전역변수라고 하겠다).
    //이 게임 내에서 게임매니저 인스턴스는 이 instance에 담긴 녀석만 존재하게 할 것이다.
    //보안을 위해 private으로.
    private static GameMgr instance = null;

    void Awake()
    {
        if (null == instance)
        {
            //이 클래스 인스턴스가 탄생했을 때 전역변수 instance에 게임매니저 인스턴스가 담겨있지 않다면, 자신을 넣어준다.
            instance = this;

            //씬 전환이 되더라도 파괴되지 않게 한다.
            //gameObject만으로도 이 스크립트가 컴포넌트로서 붙어있는 Hierarchy상의 게임오브젝트라는 뜻이지만, 
            //나는 헷갈림 방지를 위해 this를 붙여주기도 한다.
            DontDestroyOnLoad(this.gameObject);
        }
        else
        {
            //만약 씬 이동이 되었는데 그 씬에도 Hierarchy에 GameMgr이 존재할 수도 있다.
            //그럴 경우엔 이전 씬에서 사용하던 인스턴스를 계속 사용해주는 경우가 많은 것 같다.
            //그래서 이미 전역변수인 instance에 인스턴스가 존재한다면 자신(새로운 씬의 GameMgr)을 삭제해준다.
            Destroy(this.gameObject);
        }
    }

    //게임 매니저 인스턴스에 접근할 수 있는 프로퍼티. static이므로 다른 클래스에서 맘껏 호출할 수 있다.
    public static GameMgr Instance
    {
        get
        {
            if (null == instance)
            {
                return null;
            }
            return instance;
        }
    }

    public void InitGame()
    {

    }

    public void PauseGame()
    {

    }

    public void ContinueGame()
    {

    }

    public void RestartGame()
    {

    }

    public void StopGame()
    {

    }
}

```

사용 예시

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class UIMenu : MonoBehaviour
{
    //pause 버튼을 누르면 게임 일시정지
    public void OnClickBtnPause()
    {
        GameMgr.Instance.PauseGame();
    }
}
```

### 2. Monobehaviour를 상속받지 않고, Hierarchy에 존재하지 않게 하는 것

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class GameMgr
{
    //게임매니저의 인스턴스를 담는 전역변수(static 변수이지만 이해하기 쉽게 전역변수라고 하겠다).
    //이 게임 내에서 게임매니저 인스턴스는 이 instance에 담긴 녀석만 존재하게 할 것이다.
    //보안을 위해 private으로.
    private static GameMgr instance;

    //게임 매니저 인스턴스에 접근할 수 있는 프로퍼티. static이므로 다른 클래스에서 맘껏 호출할 수 있다.
    public static GameMgr Instance
    {
        get
        {
            if(null == instance)
            {
                //게임 인스턴스가 없다면 하나 생성해서 넣어준다.
                instance = new GameMgr();
            }
            return instance;
        }
    }

    //생성자를 하나 만들어줘서 원하는 세팅을 해주면 좋다.
    public GameMgr()
    {

    }

    public void InitGame()
    {

    }

    public void PauseGame()
    {
       
    }

    public void ContinueGame()
    {

    }

    public void RestartGame()
    {

    }

    public void StopGame()
    {

    }
}
```

사용 예제 : 사용법은 1번과 같다.

## Monobehaviour를 상속받지 않을 경우의 장점

1. 씬 이동시 신경을 안써도 됨. 메모리상에만 존재하게 한다면 어떤 인스턴스를 사용할지 고려하지 않아도 된다.
2. 유니티 오브젝트라면 가지는 Transform 컴포넌트를 안가져도 되어 메모리 점유를 미세하게 줄일 수 있다. 

## 싱글톤의 문제점

1. 하나의 싱글톤에 너무 많은 기능, 너무 많은 데이터를 넣으면, 훗날 프로젝트의 규모가 커졌을 때 너무 무거워질 수 있다. 
2. 클래스들과 싱글톤, 그리고 싱글톤이 가지고 있는 클래스 인스턴스간의 의존도가 복잡해져서 게임 업데이트에 어려움이 생길 수 있다.
3. 싱글톤은 게임이 종료되지 않는 한 계속해서 메모리를 점유하고 있어서, 메모리의 비효율적인 사용을 야기할 수 있다.

참고 : [https://glikmakesworld.tistory.com/2?category=797136](https://glikmakesworld.tistory.com/2?category=797136)