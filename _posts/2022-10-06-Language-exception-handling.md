---
title: "[Java] 예외처리"
author: "김소연"
date: 2022-10-06 21:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## 예외 처리(exception handling)

#### 프로그램 오류

프로그램이 실행 중 어떤 원인에 의해서 오작동을 하거나 비정상적으로 종료되는 경우가 있는데,
이러한 결과를 초래하는 원인을 `프로그램 오류/에러` 라고한다.

- 발생 시점에 따라 구분되는 오류

  | 구분                         | 설명                      |
  | -------------------------- | ----------------------- |
  | 컴파일 에러(compile-time error) | 컴파일 할 때 발생하는 에러         |
  | 런타임 에러(runtime error)      | 프로그램 실행 도중에 발생하는 에러     |
  | 논리적 에러(logical error)      | 실행은 되지만, 의도와 다르게 동작하는 것 |

- 소스코드를 컴파일하면, 컴파일러가 소스코드(.java)에 대해 오타나 잘못된 구문, 자료형 체크 등 기본적인 검사를 수행하여 오류가 있는지 알려준다. 컴파일을 성공적으로 마치면 클래스 파일(.class)이 생성되고, 생성된 클래스 파일을 실행할 수 있게 되는 것이다.

- 자바에서는 런타임 에러를 `에러(error)`와 `예외(exception)` 두 가지로 구분하였다.

  - 에러
    - 일단 발생하면 복구할 수 없는 심각한 오류 (메모리 부족(OutOfMemeryError), 스택오버플로우(StackOverflowError) 등)
    - **프로그램 코드에 의해서 수습될 수 없는 심각한 오류**
    - 비정상적인 종료가 된다.
  - 예외
    - 발생하더라도 수습될 수 있는 비교적 덜 심각한 것
    - **프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류**
    - 예외가 발생하더라도 프로그래머가 이에 대한 적절한 코드를 미리 작성해놓음으로써 비정상적인 종료를 막을 수 있다.



#### 예외 클래스의 계층 구조

자바에서는 실행 시 발생할 수 있는 오류를 클래스로 정의하였다. (Exception과 Error)

![object](/assets/img/exception.png){:width="80%" height="80%"}

예외 클래스들은 두 그룹으로 나눠질 수 있다. 

![object](/assets/img/exception2.png){:width="80%" height="80%"}

- Exception 클래스들 : **사용자의 실수**와 같은 외적인 요인에 의해 발생하는 예외
  - FileNotFoundException : 존재하지 않는 파일의 이름을 입력했을 때
  - ClassNotFoundException : 실수로 클래스의 이름을 잘못 적었을 때
  - DataFormatException : 입력한 데이터 형식이 잘못되었을 때
- RuntimeException 클래스들 : **프로그래머의 실수**로 발생하는 예외
  - ArrayIndexOutOfBoundsException : 배열의 범위를 벗어났을 때
  - NullPointerException : 값이 null인 참조변수의 멤버를 호출했을 때
  - ClassCastException : 클래스 간의 형변환을 잘못했을 때
  - ArithmeticException : 정수를 0으로 나누려고 했을 때



### 출처📎

- 자바의 정석
- [이미지 출처 - 깃허브](https://github.com/mingseok/javajungsuk3)