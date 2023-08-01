---
title: "[OS] Race condition 경쟁상태"
author: "김소연"
date: 2023-08-01 19:00:00 -0500
categories: [OS]
tags: [OS]
---



## Race condition 경쟁상태란?

Race condition이란 공유 자원에 대해 여러 개의 프로세스가 동시에 읽거나 쓰는 동작을 할 때, 공유 자원에 대한 접근이 어떤 순서로 이루어졌는지에 따라 결과값이 달라지는 상황을 말한다.
즉, Race Condition(경쟁상태)란 단어 그대로 두 개 이상의 스레드가 하나의 자원을 놓고 서로 사용하려고 경쟁하는 상황을 말한다.



## Critical Section(임계 영역)

운영체제에서 여러 프로세스가 데이터를 공유하면서 수행될 때 각 프로세스에서 공유 자원에 접근하는 프로그램 코드 부분 을 의미한다.
프로세스간에 공유자원을 접근하는데 있어서 문제가 발생하지 않도록 공유 자원의 독점을 보장해줘야 한다.



임계 영역에서는 아래 조건을 충족해야 Race condition이 발생하지 않는다.

### Mutual exclusion(상호배제)

두 개 이상의 프로세스가 공용 데이터에 동시에 접근을 하는 것을 막아야 한다. 
즉, 한 프로세스가 공용 데이터를 사용하고 있으면 그 자원을 사용하지 못하도록 막거나, 다른 프로세스가 그 자원을 사용하지 못하도록 막으면 이 문제를 피할 수 있다.

### Progress (진행)

아무도 Critical section에 있지 않다면, 진입하고자 하는 프로세스를 진입하게 해줘야 한다. (Deadlock free)
Critical section에 아무도 진입하지 못하면 안되며 다음에 어떤 프로세스가 Critical section에 진입해야 하는지는 유한한 시간에 결정되어야 한다.

#### Deadlock(교착상태)

위와 같은 상호 배제를 시행하면 추가적인 제어 문제가 발생한다. 그 중 하나가 Deadlock이다.
두 개 이상의 작업이 서로 상대방의 작업이 끝나기 만을 기다리고 있기 때문에 결과적으로 아무것도 완료되지 못하는 상태이다.



### Bounded Waiting (유한 대기)

프로세스가 Criitical section에 진입하기 위해 무한정으로 기다리는 현상이 발생해서는 안된다. (Starvation free)

#### Starvation(기아상태)

상호 배제를 시행했을 때 발생하는 또다른 문제가 Starvation이다. 
여러 프로세스가 자원을 점유할 때 특정 프로세스의 우선 순위가 낮아서 원하는 자원을 계속 할당받지 못하는 상태를 말한다.



## 예방할 수 있는 방법

이러한 race condition을 예방하는 방법은 쓰레드의 순차적 실행을 보장해주는 동기화이다.

* Software적 방법 : Peterson's Solution
* Hardware적 방법 : Mutex Locks
* Semaphores



## 출처

- [wiki/교착상태](https://ko.wikipedia.org/wiki/%EA%B5%90%EC%B0%A9_%EC%83%81%ED%83%9C)
- https://iredays.tistory.com/m/125
- [velog.io/@klloo](https://velog.io/@klloo/운영체제-경쟁-상태-Race-Condition)
- https://charles098.tistory.com/88