---
title: "[Web] 일급 함수(First-class function)"
author: "김소연"
date: 2023-07-10 19:00:00 -0500
categories: [Web Application]
tags: [Javascript]
---



## 일급 함수란?

일급 함수(First-class function)는 함수를 일급 시민(First-class citizens)으로 취급한다는 것입니다.

프로그래밍 언어에서 일급 함수를 가질 때, 함수들이 변수처럼 다루어질 수 있습니다.
(일급 객체는 객체를 변수처럼 다룰 수 있습니다.)



이 때 일급 시민의 조건은 아래와 같습니다.

> 1. 변수에 값으로 할당될 수 있다.
> 2. 다른 함수의 전달인자로 전달될 수 있다.
> 3. 다른 함수에 의해 반환될 수 있습니다.



위의 내용을 Javascript 코드를 통해 알아보겠습니다.



## 예제

### 변수에 함수 할당

```javascript
const foo = function() {
	console.log("foobar");
};

foo();	// foobar
```

변수에 익명함수를 할당한 다음, 끝에 괄호를 붙여서 함수를 호출할 해당 변수를 사용합니다.



### 함수에 전달인자로 전달

```javascript
function sayHello() {
	return "Hello, ";
}

function greeting(helloMessage, name) {
	console.log(helloMessage() + name);
}

greeting(sayHello, "Soyeon"); // Hello, Soyeon
```

`sayHello` 함수를 `greeting` 함수에 전달인자로 전달하고, 끝에 괄호`()`를 붙여서 전달인자를 사용합니다.

- 콜백함수 : 다른 함수에 전달인자로 전달되는 함수 
  (sayHello 함수는 콜백함수에 포함됩니다.)



### 함수 반환

```javascript
function sayHello() {
    return () => {
      console.log("Hello~");
    };
}
```

다른 함수로부터 함수를 반환합니다.

- 고차함수 : 함수를 반환하거나 다른 함수들을 전달인자로 사용하는 함수  
  (sayHello 함수는 고차함수에 포함됩니다.)



## 자바에서의 함수형 프로그래밍

자바 8부터는 함수형 프로그래밍을 도입하였습니다.

일반적으로 함수형 프로그래밍에서 아래 3가지 조건을 만족해야 합니다.

> 1. 순수 함수(Pure function) : 동일한 입력값에 대해 항상 같은 값을 반환한다. 전역 변수를 사용하거나 변경해서 예상치 못한 Side effect를 발생하지 않는다.
> 2. 고차함수(High Order Function) : 함수를 인자로 받거나 함수를 반환 값으로 이용할 수 있다.
> 3. 익명함수(Anonymous function) : 이름이 없는 함수를 말하며, 람다식으로 표현되는 함수 구현을 말합니다.



이러한 함수형 프로그래밍과 관련하여 도입된 기능이 있습니다.

- 람다 (+ Functional Interface)
- 스트림

이에 대한 자세한 설명은 [이 곳](https://km-so-yeon.github.io/posts/Language-Lambda-Stream/) 에서 확인하실 수 있습니다.



## 출처

- [mdn - 일급함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
- [velog.io/@vagabondms/기술-스터디-일급-함수란](https://velog.io/@vagabondms/%EA%B8%B0%EC%88%A0-%EC%8A%A4%ED%84%B0%EB%94%94-%EC%9D%BC%EA%B8%89-%ED%95%A8%EC%88%98%EB%9E%80)
- https://en.wikipedia.org/wiki/First-class_function
- https://breakout-theworld.tistory.com/50
- [warpgate3.tistory.com/entry/자바코드로-보는-함수형-프로그래밍](https://warpgate3.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%BD%94%EB%93%9C%EB%A1%9C-%EB%B3%B4%EB%8A%94-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-Functional-Programming-in-Java)
- https://tecoble.techcourse.co.kr/post/2021-09-30-java8-functional-programming/