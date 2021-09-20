---
title: "[Javascript] 자바스크립트 객체, 프로토타입"
author: "김소연"
date: 2021-09-14 22:00:00 -0500
categories: [Study, Programming]
tags: [Javascript]
---

# 기본 문법

## 생성

```javascript
var person = {
    name: 'Soyeon',
    age: 24,
    sayHi: function() {
        alert("Hi, I\'m " + this.name);
    }
};
```

- 객체 : 데이터와 함수의 집합 *person*
- 프로퍼티(속성) : 객체 내부의 속성 *name, age*
- 메소드 : 객체 내부의 함수 *sayHi*
- 객체 리터럴(object literal) : 내용을 그대로 대입해서 생성한 객체

## 접근 & 설정

1. 점 표기법
2. 괄호 표기법

```javascript
// 접근
person.age
person['age']

// 설정 (새로운 멤버 생성 가능)
person.age = 28;
person['hometown'] = 'Inchoen';
```

## this

실행 중인 코드가 속해있는 객체

```javascript
var person1 = {
    name: 'Soyeon',
    sayHi: function() {
        alert("Hi, I\'m " + this.name);
    }
}
var person2 = {
    name: 'Joy',
    sayHi: function() {
        alert("Hi, I\'m " + this.name);
    }
}

person1.sayHi();	// Hi, I'm Soyeon
person2.sayHi();	// Hi, I'm Joy
```



# 객체지향 프로그래밍

## 생성자

```javascript
// 생성
fucntion Person(name) {
    this.name = name;
    this.sayHi = function() {
        alert("Hi, I'm " + this.name);
    };
}
// 호출
var person1 = new Person("Soyeon");
var person2 = new Person("Joy");
```

* 생성자 : 프로퍼티와 메소드를 정의 *Person*
* 객체 인스턴스 : 생성자로 생성된 각각의 객체 *person1, person2*

### 생성

* `this` 키워드 사용
* 생성자 함수명은 보통 대문자로 시작

### 호출

- `new` 키워드 사용

## Object() 생성자

- 빈 객체를 생성하는 함수
- 프로퍼티와 메소드를 정의해서 생성하려면 객체 리터럴 사용

```javascript
var person1 = new Object();
var person2 = new Object({
    name: 'Soyeon',
    sayHi: function() {
        alert("Hi, I\'m " + this.name);
    }
})
```

## create() 함수

- 이미 존재하는 객체를 이용하여 새로운 객체를 생성하는 함수
- 익스플로러 8에서는 지원하지 않는다.

```javascript
var person1 = Object.create(person2);

person1.name		// Soyeon
person2.sayHi()		// Hi, I'm Soyeon
```



# 프로토타입 (prototype)





# 출처 📎

[https://developer.mozilla.org/]