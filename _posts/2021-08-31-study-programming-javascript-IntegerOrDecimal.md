---
title: "[Javascript] 자바스크립트 정수/실수 체크하기 (+소수점 몇자리 이하인지 체크하기)"
author: "김소연"
date: 2021-08-31 22:00:00 -0500
categories: [Study, Programming]
tags: [Javascript]
---


# 정수/실수 체크하기
## 산술 연산자 %

```javascript
100 % 1; 			// 0
100 % 1 === 0;		// true
100.1 % 1 === 0;	// false
```

자바스크립트에서는  `% 연산자`를 사용하면 나머지를 반환한다.

정수인 값을  `% 1`를 했을 때 무조건 0이 나오는 점을 이용해서 정수인지 실수인지 판단한다.



## Number.isInteger() 메소드 

```javascript
Number.isInteger(100);		// true
Number.isInteger(100.1);	// false
```

- 구문 : Number.isInteger(*value*)

* 반환값 : 매개변수의 값이 정수이면 true, 아니면 false를 리턴 (Boolean)

* Internet Explorer에서 지원하지 않는다.



# 응용 🏃‍♂️

```
123.4 * 100 % 1 === 0;		// true
123.45 * 100 % 1 === 0;		// true
123.456 * 100 % 1 === 0;	// false
```

`% 연산자`를 이용해서 값이 **소수점 몇자리 이하인지 체크**할 수 있다.

값이 소수점 두자리 이하인지 체크하고 싶은 경우 100을 곱해서 값을 양수로 바꿔준 다음 `% 1`을 해준다.

소수점 두자리 이상일 경우 100을 곱하더라도 여전히 상수이기 때문에 false이다.



# 요약 ⌛

- *value* % 1 === 0
- Number.isInteger(*value*)