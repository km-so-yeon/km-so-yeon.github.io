---
title: "[Java] 람다(Lambda) 표현식과 스트림(Stream)"
author: "김소연"
date: 2023-07-13 19:00:00 -0500
categories: [Language, Java]
tags: [Java]
---





Java 8부터 추가된 람다와 스트림에 대해서 알아보겠습니다.



## 람다 표현식(Lambda expression)

익명 클래스를 사용할 때 가독성이 떨어지는 단점을 보완하기 위해 만들어졌다.

- 인터페이스에 메소드가 하나인 것들만 적용 가능하다.
- 람다 표현식은 익명클래스로, 익명클래스는 람다 표현식으로 전환이 가능하다.



#### 메소드가 하나인 Java 인터페이스

- `java.lang.Runnable`

- `java.util.Comparator`

- `java.io.FileFilter`



### 람다 표현식의 구성

```java
(int x, int y) -> x + y
```

- `(int x, int y)` : 매개변수 목록
- `->` : 화살표 토큰
- `x + y` : 처리식 (한 줄 이상인 경우 중괄호로 묶을 수 있다.)



### 람다 표현식 ↔ 익명 클래스

아래와 같이 메소드가 하나인 인터페이스를
람다 표현식으로 구현했을 때와 익명 클래스로 구현했을 때 차이가 있다.

```java
interface AddClass {
    int operation(int a, int b);
}
```

익명 클래스로 처리했을 때

```java
private void calculateAnony() {
    AddClass addObject = new AddClass() {
        @Override
        public int operation(int a, int b) {
            return a + b;
        }
    };
    
    System.out.println(addObject.operation(1, 2));	// 3
}
```

람다 표현식으로 처리했을 때

```java
private void calculateLambda() {
    AddClass addObject = (a, b) -> a + b;
    System.out.println(addObject.operation(1, 2));
}
```

- a와 b처럼 변수이름을 임의로 선언해도 문제 없이 수행된다.



### Functional(기능적) 인터페이스

기능적 인터페이스는 하나의 메소드만 선언되어 있는 것을 의미한다.

```java
@FunctionalInterface
interface addClass {
    int operation(int a, int b);
}
```

`@FunctionalInterface` 어노테이션을 사용하여 명시적으로 표현할 수 있다.

- 이 인터페이스에는 내용이 없는 하나의 메소드만 선언할 수 있다.
- 만약 두 개의 인터페이스를 선언하면 컴파일 에러가 발생한다.



### 메소드 참조(더블 클론 `::`)

Java 8에서 추가된 것으로, 람다 표현식이 단 하나의 메소드만을 호출하는 경우 해당 람다 표현식에서 불필요한 매개변수를 제거하고 사용할 수 있도록 해준다.

```java
클래스이름::메소드이름
참조변수이름::메소드이름
```



#### 메소드 참조의 종류

| 종류                                                | 예                                   |
| --------------------------------------------------- | ------------------------------------ |
| static 메소드 참조                                  | ContainingClass::staticMethodName    |
| 특정 객체의 인스턴스 메소드 참조                    | containingObject::instanceMethodName |
| 특정 유형의 임의의 객체에 대한 인스턴스 메소드 참조 | ContainingType::methodName           |
| 생성자 참조                                         | ClassName::new                       |



**static 메소드 참조**

```java
DoubleUnaryOperator oper;

oper = (n) -> Math.abs(n);	// 람다 표현식
System.out.println(oper.applyAsDouble(-5));	// 5.0

oper = Math::abs;	// 메소드 참조
System.out.println(oper.applyAsDouble(-5));	// 5.0
```



**특정 객체의 인스턴스 메소드 참조**

```java
MyClass obj = new MyClass;
Function<String, Boolean> func = (a) -> obj.equals(a); // 람다 표현식
Function<String, Boolean> func = obj::equals(a);	// 메소드 참조
```



**특정 유형의 임의의 객체에 대한 인스턴스 메소드 참조**

```java
List<String> words = Arrays.asList("AB", "CD", "EF");

// 람다 표현식
words.stream().map(word -> word.toLowerCase()).forEach(word -> System.out.println(word));

// 메소드 참조
words.stream().map(String::toLowerCase).forEach(System.out::println);
```

- `map(String::toLowerCase)` : 임의의 객체 참조
  - 임의 객체(arbitary object)라는 용어가 사용된 이유는, 메소드 참조가 실행될 때마다 인자로 넘어온 값이 다를 수 있기 때문이다.
- `forEach(System.out::println)` : 인스턴스 메소드 참조



**생성자 참조**

```java
(a) -> {return new Object(a);}	// 람다 표현식
Object::new;	// 메소드 참조
```





## 스트림(Stream)

연속된 정보를 처리할 때 사용한다.

- 컬렉션에 사용할 수 있지만 배열에는 사용할 수 없다. 



#### 배열을 컬렉션으로 바꾸는 방법

**방법1.** Array 클래스의 `asList()`

```java
Integer[] values = { 1, 2, 3 };
List<Integer> list = new ArrayList<Integer>(Arrays.asList(values));
```

**방법2.** Array 클래스의 `stream()`

```java
Integer[] values = { 1, 2, 3 };
List<Integer> list = Arrays.stream(values).collect(Collectors.toList());
```



### 스트림의 구조

![stream](/assets/img/stream.jpg){:width="80%" height="80%"}

#### 1. 스트림 생성

컬렉션의 목록을 스트림 객체로 변환한다. 

- 스트림 객체 : java.util.stream 패키지의 Stream 인터페이스
- Collection 인터페이스의 `stream()`를 사용한다.
  - 더 빠르게 처리할 때는 `parallelStream()` 를 사용하면 되는데, 병렬로 처리하기 때문에 CPU도 많이 사용하고 몇개의 쓰레드로 처리할 지 보장되지 않는다. (일반적으로는 사용 X)

#### 2. 중개 연산(Intermediate operation)

생성된 스트림 객체를 사용하여 중개 연산 부분에서 처리한다.

- 이 부분에서는 아무런 결과를 리턴하지 못한다. (그래서 중개 연산이라고 한다.)
- 0개 이상의 중개 연산을 할 수 있다.

#### 3. 종단 연산(Terminal operation)

중개 연산에서 작업된 내용을 바탕으로 결과를 리턴해준다.



### 스트림의 연산자

| 연산자                              | 설명                                             |
| ----------------------------------- | ------------------------------------------------ |
| filter(pred)                        | 데이터를 조건으로 거를 때 사용                   |
| map(mapper)                         | 데이터를 특정 데이터로 변환                      |
| forEach(block)                      | for 루프를 수행하는 것 처럼 각각의 항목을 꺼냄   |
| flatMap(flat-mapper)                | 스트림의 데이터를 잘게 쪼개서 새로운 스트림 제공 |
| sorted(comparator)                  | 데이터의 정렬                                    |
| toArray(array-factory)              | 배열로 변환                                      |
| any / all / nonMatch(pred)          | 일치하는 것을 찾음                               |
| findFirst / Any(pred)               | 맨 처음이나 순서와 상관없는 것을 찾음            |
| reduce(binop) / reduce(base, binop) | 결과를 취합                                      |
| collect(collector)                  | 원하는 타입으로 데이터를 리턴                    |



#### forEach()

forEach는 종단 연산에 속하여 작업 내용을 보여주는 역할을 한다.

```java
List<StudentDto> students = new ArrayList<>();
...
students.stream().forEach(student -> System.out.println(student.getName()));
```

- `stream()` : 스트림 생성
- `forEach(student ... )` : student는 students List 객체에 담겨있는 StudentDto 객체를 의미한다.

```java
students.stream().map(student -> student.getName()).forEach(name -> System.out.println(name));
```

- `map()` : student 데이터를 변환한다.
- `forEach(name ... )` : name은 StudentDto 객체가 아니라 student.getName()의 결과인 String을 의미하게 된다.



#### map()

map은 중개 연산에 속해서 데이터를 변환하는 역할을 한다.

```java
List<Integer> intList = Array.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
intList.stream().map(x -> x * 3).forEach(System.out.println(name));
```

- `map(x -> x * 3)` : 스트림 내에 있는 값(Integer)을 3배로 변환한다.



#### filter()

filter는 중개 연산에 속해서 파라미터 값이 true인 데이터만 걸러내는 역할을 한다.

```java
List<StudentDto> students = new ArrayList<>();
...
students.stream().filter(student -> student.getScoreMath() > scoreCutLine).forEach(student -> System.out.println(student.getName()));
```

- `filter(student -> student.getScoreMath() > scoreCutLine)` : scoreCutLine 점수를 넘긴 학생들만 걸러낸다.



#### boxed()

boxed를 통해 기본 자료형을 래퍼 클래스로 Boxing할 수 있다.

```java
int[] intArr = {1, 2, 3, 4, 5};
List<Integer> intList = Arrays.stream(intArr).boxed().collect(Collectors.toList());
```



#### mapTo기본자료형()

mapTo기본자료형를 통해 래퍼 클래스를 기본 자료형으로 Unboxing할 수 있다.

```java
List<Integer> intList = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
int[] intArr = intList.stream().mapToInt(x -> x).toArray();
```

이 함수는 map과 같은 기능을 가지지만 반환형이 Stream이 아닌 IntStream이다.



### 기본 자료형 특화 스트림

Stream에서는 기본 자료형를 다룰 때 래퍼 클래스로 오토박싱이 이루어지는데, 이로 인해서 스트림이 병렬처리를 할 때 성능이 낮아진다. 

Stream 객체가 `public interface Stream<T>` 로 제네릭 파라미터`<T>`를 받는 것으로 정의되어있기 때문이다. 그렇기 때문에 Stream에서는 아래와 같은 과정이 추가되어서 병렬 스트림에서 오버헤드가 발생할 수 있다.

1. 기본 자료형을 래퍼 클래스(참조 자료형)으로 바꾸는 박싱이 일어나고
2. 다시 래퍼 클래스를 기본 자료형으로 언박싱해서 내보낸다.

그래서 오토박싱이 일어나지 않도록 성능을 개선한 기본형 특화 스트림(IntStream, DoubleStream, LogStream)을 별도로 제공한다.

#### 오토박싱 오버헤드가 발생하는 경우

```java
long n = 10;
long result = Stream.iterate(1L, i -> i + 1).limit(n).parallel().reduce(0L, Long::sum);
```

- 숫자 n까지의 합을 병렬 스트림을 이용해서 구한다.

기본형 원시타입 long이 파라미터로 들어와서 오토박싱 오버헤드가 발생한다.

#### 기본 자료형 특화 스트림을 사용한 경우

```java
long n = 10;
long result = LongStream.rangeClosed(1, n).parallel().reduce(0L, Long::sum);
```

LongStream의 rangeClosed 메소드는 기본형 타입을 다루기 때문에 오토박싱이 일어나지 않는다.



### Parallel

스트림의 `parallel()` , `parallelStream()` 메소드를 추가해주면 쉽게 병렬 처리를 할 수 있다.

```java
IntStream.range(0, 10).parallel().forEach(index -> System.out.println(Thread.currentThread().getName() + ", index=" + index + ", " + new Date()));

try{
    Thread.sleep(5000);
} catch(InterruptedException e) { }
```



#### Parallel 사용 시 고려해야 할 점

하지만 스트림은 ForkJoinPool 방식을 이용하기 때문에 분할되는 작업이 균등하게 처리되어야 한다.

- 작업을 분할하기 위해 Spliterator의 trySplit()을 사용하는데, 이 때 나누어지는 작업에 대한 비용이 높지 않아야 효율적으로 이루어질 수 있다.
- 균등하게 처리되지 않을 경우 작업들이 이루어지고 난 후 Join할 때 나머지 작업들이 기다려야되게 되므로 효과가 적을 수 있다.
- Array, ArrayList처럼 정확한 전체 사이즈를 알 수 있는 경우에는 분할 처리가 빠르고 비용이 적게들지만, LinkedList의 경우에는 효과를 찾기 어렵다.

또한 병렬로 처리되는 작업이 독립적이지 않다면, 수행 성능에 영향이 있을 수 있다.

- stream의 중간 단계 연산 중 sorted(), distinct() 와 같은 작업을 수행할 경우 내부적으로 상태에 대한 변수를 각 작업들이 공유(synchronized)하게 되어 있다. 이러한 경우에는 순차적으로 실행하는 경우가 더 효과적일 수 있다.



## 결론

이처럼 람다와 스트림을 함께 사용해서 데이터 프로세싱을 효율적으로 할 수 있고, 코드가 간결해진다.

이처럼 스트림은 데이터를 병렬로 처리할 수 있어서 for문보다 빠를 수 있다. 하지만 상황에 따라 for문은 컴파일러가 최적화하는 많은 방법이 있고 데이터 개수가 많지 않거나, 데이터가 많이 변경될 가능성이 있다면 for문이 더 빠를 수 있다. 이러한 경우를 많이 분석하여 스트림을 사용하는 것이 중요할 것 같다.



## 출처

- 자바의 신
- https://thalals.tistory.com/361
- https://m.blog.naver.com/tmondev/220945933678
- [TCP school - 메소드 참조](http://www.tcpschool.com/java/java_lambda_reference)
- https://gom20.tistory.com/217