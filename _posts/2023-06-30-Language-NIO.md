---
title: "[Java] NIO"
author: "김소연"
date: 2023-06-30 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## NIO (New IO)

JDK 1.4부터 속도 때문에 생겼다.

지금까지 사용한 스트림`Stream`을 사용하지 않고, 채널`Channel`과 버퍼`Buffer`를 사용한다.
입력받거나 출력해야 할 데이터를 항상 Buffer에 저장한다. 이를 채널이 관장하는 방식으로 진행된다.



### 파일 쓰기

```java
public void writeFile(String fileName, String data) throws Exception {
    FileChannel channel = new FileOutputStream(fileName).getChannel();
    byte[] byteData = data.getBytes();
    ByteBuffer buffer = ByteBuffer.wrap(byteData);
    channel.write(buffer);
    channel.close();
}
```

1. 파일을 쓰기 위한 FileChannel 객체를 만드려면 FileOutputStream 클래스의 `getChannel()` 메소드를 호출한다.
2. ByteBuffer 객체를 만드려면 ByteBuffer 클래스에 `wrap()` static 메소드를 호출한다. 
   이 메소드의 매개 변수는 저장할 byte의 배열을 넘겨주면 된다.
3. FileChannel 클래스에 선언된 `write()` 메소드에 buffer 객체를 넘겨주면 파일에 데이터를 쓴다.



### 파일 읽기

```java
public void readFile(String fileName) throws Exception {
    FileChannel channel = new FileInputStream(fileName).getChannel();
    ByteBuffer buffer = ByteBuffer.allocate(1024);
    channel.read(buffer);
    buffer.flip();
    while(buffer.hasRemaining()) {
        System.out.println((char)buffer.get());
    }
    
    channel.close();
}
```

1. 파일을 읽기 위한 FileChannel 객체를 만드려면 FileInputStream 클래스의 `getChannel()` 메소드를 호출한다.
2. ByteBuffer 객체를 만드려면 ByteBuffer 클래스에 `allocate()` 메소드를 호출한다. 매개변수는 데이터가 기본적으로 저장되는 크기를 넘겨준다.
3. FileChannel 클래스에 선언된 `read()`  메소드를 통해 데이터를 buffer에다 담는다.
4. `flip()`메소드는 buffer에 담겨있는 데이터의 가장 앞으로 이동한다.
5. `get()` 메소드는 한 바이트씩 데이터를 읽는 작업을 수행한다. (읽으면서 이동)
6. ByteBuffer에 선언되어 있는 `hasRemaining()` 메소드를 통해 데이터가 더 남아있는지 확인하면서 반복 작업을 수행한다.



이처럼 파일 데이터를 다룰 때 ByteBuffer라는 버퍼와 FileChannel 이라는 채널을 사용하면 간단히 처리할 수 있다.



## Buffer

NIO에서 제공하는 Buffer는 java.nio.Buffer클래스를 확장해서 사용한다.

ByteBuffer, CharBuffer, DoubleBuffer, FloatBuffer, IntBuffer, LongBuffer, ShortBuffer 등이 존재한다.



#### 버퍼의 상태 및 속성 확인

| 메소드             | 설명                                    |
| ------------------ | --------------------------------------- |
| int **capacity**() | 버퍼에 담을 수 있는 크기 리턴           |
| int **limit**()    | 버퍼에서 읽거나 쓸 수 없는 첫 위치 리턴 |
| int **position**() | 현재 버퍼의 위치 리턴                   |

- 버퍼는 데이터를 담거나, 읽는 작업을 수행하면 현재 '위치'가 이동한다. 이동하면서 그 다음 위치에 있는 것을 바로 쓰거나 읽을 수 있기 때문이다.
- 0 ≤ position ≤ limit ≤ capacity(크기)

```java
public void checkBuffer() {
    try {
        IntBuffer buffer = IntBuffer.allocate(1024);
        for(int loop = 0; loop < 100; loop++) {
            buffer.put(loop);
        }
        System.out.println(buffer.capacity());	// 1,024
        System.out.println(buffer.limit());		// 1,024 (기본크기)
        System.out.println(buffer.position());	// 100
    }
}
```





#### 위치를 변경하는 메소드

| 메소드                     | 설명                                                         |
| -------------------------- | ------------------------------------------------------------ |
| Buffer **flip**()          | limit 값을 현재 position으로 지정한 후, position을 0(가장 앞)으로 이동 |
| Buffer **mark**()          | 현재 position을 mark                                         |
| Buffer **reset**()         | 버퍼의 position을 mark한 곳으로 이동                         |
| Buffer **rewind**()        | 현재 버퍼의 position을 0으로 이동                            |
| int **remaining**()        | limit - position 계산 결과를 리턴                            |
| boolean **hasRemaining**() | position 과 limit 값에 차이가 있을 경우 true를 리턴          |
| Buffer **clear**()         | 버퍼를 지우고 현재 position을 0으로 이동하며, limit 값을 버퍼의 크기로 변경 |

- flip()은 limit값을 변경하지만 rewind()는 변경하지 않는다.
- limit값까지만 사용하고자 할 때 remaining(), hasRemaining()을 사용한다.
- mark 값 위치
  - 0 ≤ mark ≤ position ≤ limit ≤ capacity(크기)



## IO와 NIO 비교

| 구분                    | IO              | NIO                               |
| ----------------------- | --------------- | --------------------------------- |
| 입출력 방식             | 스트림 방식     | 채널 방식                         |
| 버퍼 방식               | 넌버퍼          | 버퍼                              |
| 비동기 방식             | 지원 안함       | 지원                              |
| Blocking / Non-Blocking | Blocking만 지원 | Blocking / Non-Blocking 모두 지원 |



### 결론

**NIO**는 연결 클라이언트 수가 많고 하나의 입출력 처리 작업이 오래걸리지 않는 경우에 사용하는 것이 좋다.
스레드에서 입출력 처리가 오래 걸린다면 대기하는 작업의 수가 늘어나므로 제한된 스레드로 처리하는 것이 불편할 수 있다.

**IO**는 대용량 데이터 처리가 필요하고, 순차적으로 처리되어야할 경우 사용하는 것이 좋다.



## 출처

- 자바의 신
- [알통몬의 인생 - IO와 NIO의 차이점](https://m.blog.naver.com/rain483/220636709530)
- [gyulee0220 - Java NIO](https://adrian0220.tistory.com/144)