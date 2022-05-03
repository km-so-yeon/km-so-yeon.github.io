---
title: "[Python] 파이썬 기본 문법 for 코딩테스트"
author: "김소연"
date: 2022-04-13 21:00:00 -0500
categories: [Language]
tags: [Python]
---





알고리즘 공부를 위해 간단하게 정리하는 파이썬 기본 문법입니다.



## 자료형

### 숫자형

- 정수형

  ```
  a = 123
  a = -123
  ```

- 실수형

  ```
  a = 1.2			// 소수점 표현 방식
  a = 4.24e10		// 지수 표현 방식, 대문자 E도 가능 (4.24 * 10^10)
  ```

- 8진수

  ```
  a = 0o177		// 0o, 0O로 시작
  ```

- 16진수

  ```
  a = 0x8ff		// 0x로 시작
  ```

- 연산자

  - 사칙연산 : `+`, `-`, `*`, `/`
  - 제곱 : `**`
  - 나머지 : `%`
  - 몫 : `//`



### 문자형

#### 생성

```
a = 'hello'
a = "hello"
a = '''hello'''
a = """hello"""

a = '"hello"'		// "hello"
a = '\'hello\''		// 'hello'
a = """hello
world"""
// hello
world
```

- `'`, `"` 포함시키기
  - `'` 포함시킬 경우 `"` 를, `"` 포함시킬 경우 `'` 사용
  - `\` 사용
- 여러 줄인 문자열
  - `\n` (이스케이프 코드) 사용
  - `'''`, `"""` 사용

#### 접근 (인덱싱, 슬라이싱)

```
a[4]		// o
a[0:3]		// hel
a[-2]		// l
```

- 0부터 시작
- [시작:끝] : 끝 글자 전까지 return (시작 <= a < 끝)
- 음수일 경우 마지막 글자부터 -1

#### 연산

```
a = "hello"
a += " world"		// "hello world"
a * 2				// "hello worldhello world"
```

- 연결 :  `+`
- 반복 : `*`
- 길이 : `len(a)`

#### 포매팅

- `%` 사용

  ```
  "I eat %d apples." % 3			// I eat 3 apples.
  "I eat %d apples." % "three"	// I eat three apples.
  number = 10
  "I ate %d apples. so I was sick for %s days." % (number, "three")	
  // "I ate 10 apples. so I was sick for three days."
  
  "%10s" % "hi"					// '        hi'
  "%-10sjane" % "hi"				// 'hi        jane'
  "%0.4f" % 3.42134234			// '3.4213'
  ```

  - 숫자, 문자열, 변수, 2개 이상의 값 대입
  - 문자열 포맷 코드 사용 : %d, %s ..
  - 공백 : `숫자` (문자열의 전체 길이)
  - 정렬 : `-` 왼쪽 정렬, 기본 오른쪽 정렬
  - 소수점 : `.숫자`  (소수점 아래 자리 수)

- `format` 함수 사용

  ```
  "I eat {0} apples".format(3)
  "I eat {0} apples".format("five")
  number = 10
  "I ate {0} apples. so I was sick for {1} days.".format(number, "three")
  "I ate {number} apples. so I was sick for {day} days.".format(number=10, day=3)
  "I ate {0} apples. so I was sick for {day} days.".format(10, day=3)
  
  "{0:>10}".format("hi")			// '        hi'
  "{0:=>10}".format("hi")			// '========hi'
  "{0:0.4f}".format(3.42134234)	// '3.4213'
  "{{ and }}".format()			// '{ and }'
  ```

  - 숫자, 문자열, 변수, 2개 이상의 값, 이름형태로 대입 (이름형태, 인덱스 혼용 가능)
  - 공백 : `숫자`
  - 정렬 : `:<` 왼쪽정렬, `:>` 오른쪽정렬, `:^` 가운데정렬
  - 공백채우기 : `:` 과 정렬 문자`<`, `>`, `^` 사이의 문자값
  - 소수점 : `:0.숫자f`
  - `{`, `}` 문자 : `{{`, `}}`

- `f` 사용

  - 파이썬 3.6 버전 이상부터 사용 가능

  ```
  name = "김소연"
  age = 25
  f'내 이름은 {name}입니다. 나이는 {age}입니다.'	 // '내 이름은 김소연입니다. 나이는 25입니다.'
  f'내년에는 {age+1}이 됩니다.'					// '내년에는 26이 됩니다.'
  
  d = {'name' : '김소연', 'age' : 25}
  f'내 이름은 {d['name']}입니다. 나이는 {d['age']}입니다.'	 // '내 이름은 김소연입니다. 나이는 25입니다.'
  
  f'{"hi":>10}'					// '        hi'
  f'{"hi":=>10}'					// '========hi'
  f'{3.4213:0.4f}'				// '3.4213'
  f'{{ and }}'					// '{ and }'
  ```

  - 변수 대입
  - 표현식 사용 가능 (변수값으로 연산)
  - 딕셔너리 대입
  - 공백, 정렬, 소수점, `{`, `}` 문자는 format 함수와 동일

#### 문자열 관련 함수

- 문자 개수 세기 : `a.count('l')`
- 위치 알려주기
  - `a.find('h')` : 문자가 처음 나온 위치 리턴, 없을 경우 -1 리턴
  - `a.index('o')` : 문자가 처음 나온 위치 리턴, 없을 경우 오류
- 문자열 삽입 : `",".join('abcd')`

- 대문자로 바꾸기 : `a.upper()`
- 소문자로 바꾸기 : `a.lower()`
- 왼쪽 공백 지우기 : `a.lstrip()`
- 오른쪽 공백 지우기 : `a.rstrip()`
- 양쪽 공백 지우기 : `a.strip()`
- 문자열 바꾸기 : `a.replace("Life", "Your leg")`
- 문자열 나누기 : `a.split()`
  - 문자열을 파라미터 기준으로 나눠서 리스트로 리턴
  - 파라미터 없을 경우 공백 문자 기준



### 리스트

#### 생성

```
a = [1, 2, 3, 4]
b = [1, 2, 3, ['a', 'b']]
```

#### 접근 (인덱싱, 슬라이싱)

```
a[0]		// 1
a[0:2]		// [1, 2]
```

#### 연산

- 연결 :  `+`
- 반복 : `*`
- 길이 : `len(a)`

#### 수정

```python
a[1] = "A"
```

#### 삭제

```python
a = [1, 2, 3, 1, 2, 3]
del a[4]	// [1, 2, 3, 1, 3]
del a[4:]	// [1, 2, 3, 1]
```

- del 객체

```python
a.remove(1)	// [2, 3, 1]
```

- remove(값) : 첫 번째로 나오는 값을 가진 요소 삭제

```python
a.pop()		// [2, 3]
a.pop(1)	// [3], 2 리턴
```

- pop() : 마지막 요소 삭제
- pop(순서) : 해당 순서의 요소 삭제 후 리턴

#### 추가

```python
a = [1, 2, 3]
b = ['a', 'b']
a.append(4)		// [1, 2, 3, 4]
a += b			// [1, 2, 3, 4, 'a', 'b']
a.extend(b)		// [1, 2, 3, 4, 'a', 'b', 'a', 'b']
a.insert(0, 0)	// [0, 1, 2, 3, 4, 'a', 'b']
```

- append()
- `+` 연산
- extend(리스트)
- insert(위치, 값)

#### 리스트 관련 함수

- 정렬
  - a.sort() : 오름차순 정렬
  - a.sort(reverse=true) : 내림차순 정렬
- 뒤집기
  - a.reverse() : 현재 리스트를 거꾸로 뒤집기
- 찾기
  - a.index(값) : 값이 있는 위치를 리턴
- 개수 세기
  - a.count(값) : 값이 몇 개 있는지 리턴



### 튜플

값 추가, 수정, 삭제 불가

#### 생성

```python
a = (1, 2, 3)
a = 1, 2, 3
a = (1, 2, 3, ('a', 'b'))
a = (1, )
```

- 1개의 요소만을 가질 때는 콤마 `,` 를 반드시 붙여야 한다. (안붙이면 튜플이 아닌 변수 취급)
- 괄호를 생략해도 된다.

#### 접근 (인덱싱, 슬라이싱)

```python
a = (1, 2, 3, 'a', 'b')
a[1]		// 2
a[2:4]		// (2, 3, 'a')
```

#### 연산

- 연결 :  `+`
- 반복 : `*`
- 길이 : `len(a)`



### 딕셔너리

Key를 통해 Value를 찾는다.

#### 생성

```python
a = {'name' : '김소연', 'age' : 25}
a = {'a' : [1, 2, 3]}
```

- Key : Value
- Key : 변하지 않는 값을 넣어야 한다. (숫자, 문자열, 튜플)
- Value : 아무 값이나 넣을 수 있다.

#### 접근

```python
a = {'name' : '김소연', 'age' : 25}
a['name']		// '김소연'
a.get('age')	// 25
```

- `[]`  : 값이 없을 경우 오류 발생
- get() : 값이 없을 경우 None 리턴

#### 추가

```python
a['color'] = 'blue'
```

#### 삭제

````python
del a['color']
````

#### 딕셔너리 관련 함수

- Key 찾기 : `'name' in a`
- 리스트 만들기
  - 키 리스트 : a.keys()
  - 값 리스트 : a.values()
  - 키, 값 튜플쌍 리스트 : a.items()
- 키, 값 모두 지우기 : `a.clear()`



### 집합

- 순서가 없다.
- 중복을 허용하지 않는다.

#### 생성

```python
a = {1, 2, 3, 4, 5}
a = set([1, 2, 3, 4, 5])
```

#### 접근

```python
a = set([1, 2, 3])
l = list(a)			// [1, 2, 3]
l[0]				// 1
t = tuple(a)		// (1, 2, 3)
t[0]				// 1
```

- 순서가 없기 때문에 인덱싱으로 접근 못한다.
- 리스트나 튜플로 변환 후 접근 가능하다.
  - list()
  - tuple()

#### 집합 연산

```python
a = {1, 2, 3, 4, 5}
b = {4, 5, 6, 7, 8}
a & b
a | b
a - b
a.intersection(b)
a.union(b)
a.difference(b)
```

- 교집합
  - &
  - intersection()
- 합집합
  - |
  - union()
- 차집합
  - -
  - difference()

#### 추가

```python
a = {1, 2, 3}
a.add(4)				// {1, 2, 3, 4}
a.update([5, 6])		// {1, 2, 3, 4, 5, 6}
```

- add() : 하나 추가
- update() : 여러 개 추가

#### 삭제

```python
a = {1, 2, 3}
a.remove(2)				// {1, 3}
```

- remove() : 특정 값 제거



### 불

- 참(True), 거짓(False)를 나타내는 자료형

- 첫 문자를 항상 대문자로 사용

```python
a = True
b = False
```

#### 자료형의 참과 거짓

- 문자열, 리스트, 튜플, 딕셔너리 등의 값이 비어있을 때 거짓, 비어있지 않으면 참
- 숫자 `0` 일 때 거짓
- `None`은 거짓

### 불 연산

- bool()

```python
bool('python')		// True
bool('')			// False
```



## 제어문

### if문

```python
if <조건문1> :
	<수행할 문장1>
elif <조건문2> :
	<수행할 문장2>
else :
	<수행할 문장3>
```



### while문

```python
while <조건문> :
	<수행할 문장>
```

- break, continue 사용



### for문

```python
for 변수 in 반복가능객체 :
	<수행할 문장>
```

- 반복가능객체 : 리스트, 튜플, 문자열

  - 객체 안의 요소를 변수에 대입하면서 반복된다.

- break, continue 사용

- range() 사용 (객체 대신)

  ```python
  for x in range(a, b) :
  	...
  ```

  - a부터 b-1까지 x에 대입

- 리스트 내포

  ```python
  [표현식 for 변수 in 반복가능객체 if 조건문]
  
  a = [1, 2, 3, 4]
  result = [num * 3 for num in a]				// [3, 6, 9, 12]
  result = [x * y for x in range(2, 10)
           		for y in range(1, 10)]		// 구구단의 결과
  ```

  - 리스트 안에 for문을 포함
  - if문 생략 가능
  - for문 2개 이상 사용 가능



## 함수

#### 구조

```python
def 함수명(매개변수) :
	<수행할 문장>
```



#### 매개변수

- 매개변수와 인수

  ```python
  def add(a, b) :		// a, b는 매개변수
  	return a + b
  	
  pring(add(3, 4))	// 3, 4는 인수
  ```

- 매개변수 지정

  ```python
  def add(a, b) :
  	return a + b
  	
  result = add(b = 5, a = 3)
  ```

  - 순서에 상관없이 사용할 수 있다.

- 매개변수 여러 개

  - `*` 붙이기 : 여러 개의 입력값을 **튜플**로

    ```python
    def add_many(*args) :
    	result = 0
    	for i in agrs :
    		result = result + i
    	return result
    	
    result = add_many(1, 2, 3)		// 6 , args는 (1, 2, 3)
    ```

  - `**` 붙이기 : `key=value` 형태의 입력값을 **딕셔너리**로

    ```python
    def pring_kwargs(**kwargs) :
    	print(kwargs)
    	
    print_kwargs(a=1)				// {'a' : 1}
    ```

- 매개변수 초깃값 설정

  ```python
  def say_myself(name, old, woman=True) :
  	print("이름은 %s입니다." % name)
  	print("나이는 %d입니다." % old)
  	if woman :
  		print("여자입니다.")
  	else :
  		print("남자입니다.")
  
  say_myself("김소연", 25)
  ```

  - 초기화하고 싶은 매개변수는 항상 뒤쪽에 위치해야 한다.



#### #### 함수 안에서 함수 밖의 변수 사용

- return 사용

  ```python
  a = 1
  def vartest(x) :
  	x = x + 1
  	return x
  
  a = vartest(a)
  ```

- global 사용

  ```python
  a = 1
  def vartest() :
  	global a
  	a = a + 1
  	
  vartest()
  ```

  - 함수 안에서 함수 밖의 변수를 직접 사용하겠다는 뜻
  - 함수는 독립적으로 존재하는 것이 좋기 때문에 사용하지 않는 것이 좋다. (return 사용할 것)



#### lambda

```
lambda 매개변수 : 표현식
```

- 함수를 생성할 때 사용하는 예약어 (def와 동일한 역할)

- 함수를 한줄로 간결하게 만들 때 사용

  ```python
  add = lambda a, b : a + b
  result = add(3, 4)		// 7
  ```



## 입력/출력

### 입력 input()

```python
number = input("숫자를 입력하세요 : ")
// 숫자를 입력하세요 : 3
type(number)
// <class 'str'>
```

- input으로 입력되는 모든 것은 문자열로 취급



### 출력 print()

- 큰따옴표(")로 둘러싸인 문자열은 + 연산과 동일

  ```python
  print("life" "is" "too short")		// lifeistoo short
  print("life" + "is" + "too short")	// lifeistoo short
  ```

- 문자열 띄어쓰기 - 콤마(,) 사용

  ```python
  print("life", "is", "too short")	// life is too short
  ```

- 끝 문자 지정 (end)

  ```python
  for i in range(10) :
  	print(i, end=' ')
  	
  // 0 1 2 3 4 5 6 7 8 9
  ```

  - 한 줄에 이어서 출력한다.



### 출처📎

[위키독스-점프 투 파이썬](https://wikidocs.net/book/1)