---
title: "Lv1 단순한 문제들"
excerpt: ""
toc: true
toc_sticky: true
toc_label: "목차"
categories:
  - CodingTest
tags:
  - [CPP, CodingTest]
date: 2021-09-15
breadcrumb: true
---

# 단순한 문제

## 내적

### 문제

[코딩테스트 연습 - 내적](https://programmers.co.kr/learn/courses/30/lessons/70128)

### 풀이

a와 b의 내적을 구하는 방법은 내적의 공식인은 a * b 이다.

주어진 배열을 순회하면서 내적을 구해 더해보자.

### 코드

```cpp
#include <string>
#include <vector>

using namespace std;

int solution(vector<int> a, vector<int> b) {
    int answer = 0;
    for(int i = 0; i<a.size(); i++)
        answer += a[i] * b[i];
    return answer;
}
```

## 음양 더하기

### 문제

[코딩테스트 연습 - 음양 더하기](https://programmers.co.kr/learn/courses/30/lessons/76501)

### 풀이

두 배열이 주어진다. 절댓값을 담은 정수배열과 해당 정수에 매칭되는 부호를 담은 불리언 배열이다. 같은 인덱스에 존재하는 원소끼리 값을 구해 answer에 더해주자.

### 코드

```cpp
#include <string>
#include <vector>

using namespace std;

int solution(vector<int> absolutes, vector<bool> signs) {
    int answer = 0;
    for(int i=0; i< absolutes.size(); i++)
        answer += signs[i] ? absolutes[i] : -absolutes[i];
    return answer;
}
```

## 약수의 개수와 덧셈(Lv.1)

### 문제

[코딩테스트 연습 - 약수의 개수와 덧셈](https://programmers.co.kr/learn/courses/30/lessons/77884)

### 풀이

말그대로 주어진 숫자의 약수를 구해서 짝수인지 홀수인지 판단하고 해당 숫자를 더하거나 빼면 된다.

### 코드

```cpp
#include <string>
#include <vector>
#include <iostream>

using namespace std;
int cal(int i)
{
    int cnt = 0;
    for(int num=1; num<=i; num++)
    {
        if(i%num == 0)
            cnt++;
    }
    return cnt%2 == 0 ? i : -i; 
}

int solution(int left, int right) {
    int answer = 0;
    for(int i = left; i <= right; i++)
    {
        answer += cal(i);
    }
    return answer;
}
```

## 부족한 금액 계산하기(Lv.1)

### 문제

[코딩테스트 연습 - 1주차_부족한 금액 계산하기](https://programmers.co.kr/learn/courses/30/lessons/82612)

### 풀이

비용(price)에 사용 횟수(count)를 곱한 값을 0 ~ count까지 더하여 총액을 구하고 마지막에 소지금(money)와 비교하여 소지금이 모자라면 차액을 아니면 0을 제출한다.

### 코드

```cpp
#include <string>
#include <vector>
#include <iostream>

using namespace std;
int cal(int i)
{
    int cnt = 0;
    for(int num=1; num<=i; num++)
    {
        if(i%num == 0)
            cnt++;
    }
    return cnt%2 == 0 ? i : -i; 
}

int solution(int left, int right) {
    int answer = 0;
    for(int i = left; i <= right; i++)
    {
        answer += cal(i);
    }
    return answer;
}
```