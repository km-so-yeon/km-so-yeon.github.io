---
title: "[Javascript] 자바스크립트 기본 요약"
author: "김소연"
date: 2021-09-01 22:00:00 -0500
categories: [Language]
tags: [Javascript]
---

(계속 UPDATE 예정)

# 변수

```javascript
var hometown = "Inchoen";
let age;
const MY_NAME = "soyeon";

function sayHello() {
    hello = "javascript";
}
```
- 변수에 초기화되기 전의 값 : undefined
- var : function scope, hoisting (유효범위를 최상단으로 끌어올림-선언 전에 사용 가능)
- let : block scope, hoisting 안됨
- const : 상수, block scope
- 함수 내에서 선언 명령어 제외하고 선언 시 - 전역 변수로 사용됨



## 변수의 종류
- 숫자 : 10진수(정수/실수)
- 문자열 : `'` (작은따옴표) 혹은 `"`(큰따옴표)
- Boolean : true, false
- 배열 : `[]` (대괄호)
- 객체 : `{}` (중괄호)

# 함수
```javascript
// 함수 선언
function add(x, y) {
	return x + y;
}
// 함수 호출
add(1, 2);			// 3

// 익명 함수
function() {
	alert("Hi, I\'m soyeon");
}
// 화살표 함수
(name) => {
	alert("Hi, I\'m " + name);
};
// 함수 표현식
var sayHi = function(name) {
	alert("Hi, I\'m " + name);
}
// 함수 표현식 호출
sayHi(soyeon);		// Hi, I'm soyeon
```
- 함수 선언 : hoisting
- 함수 표현식 : hoisting 안됨
- 익명함수, 화살표함수 : 함수를 재사용하지 않을 경우(이벤트 핸들러 내에서 등등)
