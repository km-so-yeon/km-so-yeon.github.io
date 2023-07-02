---
title: "[Java] JDK에 따른 ArrayList 비교"
author: "김소연"
date: 2023-06-28 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



ArrayList 클래스에 주요 메소드로는 생성자, get, add, remove등이 있습니다.

그 중에서도 add 메소드의 변화가 성능에 미치는 영향이 가장 크다고 느꼈기 때문에 add메소드를 비교해보겠습니다.



## JDK 6

### add메소드

```java
    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacity(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    /**
     * Increases the capacity of this <tt>ArrayList</tt> instance, if
     * necessary, to ensure that it can hold at least the number of elements
     * specified by the minimum capacity argument.
     *
     * @param   minCapacity   the desired minimum capacity
     */
    public void ensureCapacity(int minCapacity) {
        modCount++;
        int oldCapacity = elementData.length;
        if (minCapacity > oldCapacity) {
            Object oldData[] = elementData;
            int newCapacity = (oldCapacity * 3)/2 + 1;
                if (newCapacity < minCapacity)
            newCapacity = minCapacity;
                // minCapacity is usually close to size, so this is a win:
                elementData = Arrays.copyOf(elementData, newCapacity);
        }
    }
```

#### add 처리과정

`ensureCapacity()`에서 ArrayList 확장

현재 늘어난 길이 `minCapacity`가 기존 배열의 길이 `oldCapacity = elementData.length`보다 클 경우 ArrayList를 확장한다.

#### ArrayList 확장 과정

> 1. 새로운 길이`newCapacity`를 3배하고 2로 나눈 후(약 1.5배) 1을 더한값으로 지정한다.
>
> 2. 만약 `newCapacity`도 `minCapacity`보다 작을 경우 `newCapacity`를 `minCapacity`로 지정한다.



#### 특징

JDK 6에서는 새로운 길이를 **곱셈과 나눗셈 연산**을 통해 지정한다.



## JDK 7

### add메소드

```java
    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == EMPTY_ELEMENTDATA) {
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

#### add 처리과정

`ensureCapacityInternal()`에서 elementData가 비었을 때 사용되는 `EMPTY_ELEMENTDATA`인 경우
현재 늘어난 길이`minCapacity`를 `DEFAULT_CAPACITY`(==10)과 `minCapacity` 중 큰 수로 지정한다.

```java
    /**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;

	/**
     * Shared empty array instance used for empty instances.
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};
```

`ensureExplicitCapacity()`에서 `modCount`를 늘려주고
현재 늘어난 길이 `minCapacity`가 기존 배열의 길이 `oldCapacity = elementData.length`보다 클 경우
`grow()`에서 ArrayList를 확장한다.

- modCount : ArrayList 자료구조에 얼마나 수정이 되었는지 횟수를 체크하는 필드 
  (AbstractList 클래스에 있는 필드)



#### ArrayList 확장과정

> 1. 새로운 길이 `newCapacity` 에 기존 배열의 길이`oldCapacity` + (기존 배열의 길이를 오른쪽으로 한칸 shift한 값)로 지정한다. (오른쪽으로 한칸 shift == 나누기 2)
>    👉약 기존 배열의 길이 * 1.5
>
> 2. `newCapacity`가 `minCapacity`보다 작을 경우 `newCapacity`를 `minCapacity`로 지정한다.
>
> 3. `newCapacity`가 `MAX_ARRAY_SIZE`보다 클 경우 `hugeCapacity()`의 리턴값으로 설정한다.
>    👉overflow인 경우 OutOfMemoryError를 던지고,
>    overflow가 아닌 경우 minCapacity가 MAX_ARRAY_SIZE보다 크면 Integer.MAX_VALUE를, 작거나 같으면 MAX_ARRAY_SIZE를 돌려준다.



#### 특징

JDK 7에서는 **비트 연산**을 통해 효율적으로 계산한다.

또한 **overflow에 대한 exception handling**이 추가되었다.



## JDK 8

### add메소드

```java
    /**
     * Appends the specified element to the end of this list.
     *
     * @param e element to be appended to this list
     * @return <tt>true</tt> (as specified by {@link Collection#add})
     */
    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    private void ensureCapacityInternal(int minCapacity) {
        ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
    }

	private static int calculateCapacity(Object[] elementData, int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
            return Math.max(DEFAULT_CAPACITY, minCapacity);
        }
        return minCapacity;
    }

    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

`ensureCapacityInternal()`에서 1차적으로 `calculateCapacity()`의 결과값으로 늘어날 크기를 계산한다.



`calculateCapacity()`에서는 처음 객체가 생성될 때 default size로 생성되는 array 변수`DEFAULTCAPACITY_EMPTY_ELEMENTDATA`일 경우 현재 늘어난 길이`minCapacity`를 `DEFAULT_CAPACITY`(==10)과 `minCapacity` 중 큰 수로 지정한다.

```java
    /**
     * Shared empty array instance used for empty instances.
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access
```

`EMPTY_ELEMENTDATA`는 객체가 비었을 때 사용하는 변수이고 `DEFAULTCAPACITY_EMPTY_ELEMENTDATA`는 객체가 default size로 생성되었고 비어있는 변수를 나타낸다. (기본 생성자로 객체를 생성할 때 사용된다.)



`ensureExplicitCapacity()`에서부터는 Java 7과 동일하다.



#### 특징

객체 크기가 0일 때와 객체 크기가 DEFAULT_CAPACITY (= 10) 일 때를 구분한다.

이는 add 메서드만 봤을 때는 크게 차이가 없어보이지만 생성자에서 차이가 나타난다.

**JDK 7** ArrayList 기본 생성자

```java
    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        super();
        this.elementData = EMPTY_ELEMENTDATA;
    }
```

**JDK 8** ArrayList 기본 생성자

```java
    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```

이렇게 구분해줌에 따라서 JDK 8부터는
기본 생성자로 생성하면 미리 초기화 된 빈 배열이 할당되고 add할 때 default size로 배열을 초기화한다.

만약 생성자에 초기 size를 전달하면 그 크기만큼 배열이 할당되고
초기 size를 0으로 지정해준다면 미리 초기화 된 빈 배열이 할당되고 add할 때 크기가 1부터 늘어난다.



## 결론

개발하면서 ArrayList를 간편하게 사용하였는데 뒤에서는 이렇게 성능을 향상시키고 메모리를 더 안전하게 사용하기 위해 노력해왔다는 것을 알 수 있었습니다. 

JDK 버전을 선택할 때 다른 고려했을 만한 요소들이 있겠지만 이렇게 살펴보고나니 JDK 8 버전을 왜 많이 사용하는지 느낄 수 있었습니다.