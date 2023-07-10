---
title: "[Web] 일차 함수(First-class function)"
author: "김소연"
date: 2023-07-10 19:00:00 -0500
categories: [Web Application]
tags: [Javascript]
---



## 일차 함수란?

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



## 결론

이처럼 함수를 변수처럼 다룰 수 있다면(일급 함수로 취급하면) 해당 언어는 **함수형 프로그래밍 언어가 가능**해진다고 합니다.

일급 객체, 일급 함수를 사용하는지 여부에 따라 언어들을 아래와 같이 나눌 수 있습니다.

- 함수가 일급 시민인 언어 : Javascript, Scala, Go 등
- 함수가 일급 시민이 아닌 언어 : C, Java 등



## 출처

- [mdn - 일급함수](https://developer.mozilla.org/ko/docs/Glossary/First-class_Function)
- [velog.io/@vagabondms/기술-스터디-일급-함수란](https://velog.io/@vagabondms/%EA%B8%B0%EC%88%A0-%EC%8A%A4%ED%84%B0%EB%94%94-%EC%9D%BC%EA%B8%89-%ED%95%A8%EC%88%98%EB%9E%80)
- https://en.wikipedia.org/wiki/First-class_function
- https://breakout-theworld.tistory.com/50