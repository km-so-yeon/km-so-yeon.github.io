---
title: "[Java] File"
author: "김소연"
date: 2023-06-25 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



## I/O (Input Output)

프로그램에 있는 내용을 **파일에 읽거나 저장할 일이 있을 때, 다른 서버나 디바이스로 보낼 일이 있을 때** 사용한다.



### Java의 I/O 처리

- 초기 단계 : java.io 패키지에 있는 클래스만 제공
  - 여러 종류의 Stream 클래스 제공
    - 읽는 작업은 InputStream, 쓰는 작업은 OutputStream을 통해서 작업
  - Reader, Writer 클래스를 통해 char 기반의 문자열로만 되어 있는 파일 처리
- JDK 1.4 이상 : NIO(New I/O) 추가
  - Buffer와 Channel 기반으로 데이터 처리
- Java 7 이상 : NIO2 추가



## File 클래스

자바의 java.io 패키지에 있으며, 파일 및 경로 정보를 통제하기 위한 클래스이다.

- 정체가 불분명하고, 심볼릭 링크와 같은 유닉스 계열의 파일에서 사용하는 몇몇 기능을 제대로 제공하지 못한다. 그래서 Java 7부터 java.nio.file 패키지에 있는 Files라는 클래스에서 File 클래스에 있는 메소드들을 대체하여 제공한다.



### 생성자

| 생성자                            | 설명                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| File(File parent, String child)   | 이미 생성되어 있는 File 객체(parent)와 그 경로의 하위 경로 이름으로 새로운 File 객체를 생성한다. |
| File(String pathname)             | 지정한 경로 이름으로 File 객체를 생성한다.                   |
| File(String parent, String child) | 상위 경로(parent)와 하위 경로(child)로 File 객체를 생성한다. |
| File(URI uri)                     | URI에 따른 File 객체를 생성한다.                             |

File 객체를 생성할 때 child나 pathname은 경로가 될 수도 있고, 파일 이름이 될 수도 있다.



```java
public class FileSample {
    public static void main(String[] args) {
        FileSample sample = new FileSample();
        String pathName = "C:" + File.separator + "Users" + File.separator + "text";
        sample.checkPath(pathName);
    }
    public void checkPath(String pathName) {
        File file = new File(pathName);
        System.out.println(pathName + " is exists? " + file.exists());
    }
}
```





## 파일의 경로와 상태 확인

#### `File.separator` 디렉터리를 구분하는 기호

- 경로를 나타낼 때 역슬래시 혹은 원(₩) 기호를 한 번만 사용한다.
  - 자바에서는 String 안에 역슬래시를 한 번만 쓰면 미리 약속한 특수 기호(Escape character)로 인식한다. 따라서 역슬래시를 나타내기 위해서 **역슬래시를 연달아서 `\\`사용**해야 한다.

- OS마다 디렉터리를 구분하는 기호가 다르다.
  - 유닉스 계열은 /로 구분

👉 File 클래스의 **separator** static 변수 사용 



#### 디렉터리 존재 여부 확인

| 메소드           | 설명                                                         |
| ---------------- | ------------------------------------------------------------ |
| boolean exists() | 해당 디렉터리가 존재하면 true, 존재하지 않으면 false를 리턴한다. |



#### 디렉터리 생성

| 메소드           | 설명                              |
| ---------------- | --------------------------------- |
| boolean mkdir()  | 디렉터리를 하나만 만든다.         |
| boolean mkdirs() | 여러 개의 하위 디렉터리를 만든다. |

예) C:\Users 까지 있을 때 C:\Users\text1\text2 를 만드려고하면 mkdir()는 해당 경로에 디렉터리가 만들어지지 않고, mkdirs()는 text1과 text2 디렉터리를 만든다.



#### 해당 객체가 경로/파일/숨겨진 파일인지 확인

| 메소드                | 설명                                         |
| --------------------- | -------------------------------------------- |
| boolean isDirectory() | 해당 객체가 경로를 나타내면 true를 리턴한다. |
| boolean isFile()      | 해당 객체가 파일을 나타내면 true를 리턴한다. |
| boolean isHidden()    | 해당 객체가 숨겨진 파일이면 true를 리턴한다. |



#### 해당 객체에 권한 확인

| 메소드               | 설명                                                         |
| -------------------- | ------------------------------------------------------------ |
| boolean canRead()    | 해당 객체에 읽을 수 있는 권한이 있으면 true를 리턴한다.      |
| boolean canWrite()   | 해당 객체에 쓸 수 있는 권한이 있으면 true를 리턴한다.        |
| boolean canExecute() | 해당 객체를 실행할 수 있는 권한이 있으면 true를 리턴한다. (Java 6부터 추가되었다.) |

 

#### 파일 생성시간 확인

| 메소드              | 설명                                                         |
| ------------------- | ------------------------------------------------------------ |
| long lastModified() | 파일이나 경로가 언제 생성되었는지 현재 시간을 리턴해준다.<br />- Date 클래스를 사용하여 시간을 확인하면 된다. |



## 파일 처리

#### 파일 생성

`boolean createNewFile()`

비어있는 새로운 파일을 생성한다.

- `IOException`을 던진다고 정해져있어서 try-catch로 묶어주어야 한다.
- 새로 파일을 생성하는 데 성공했을 경우 true, 이미 파일이 존재할 경우 false를 리턴한다.



#### 파일 정보 확인

File로 끝나는 메소드들은 File 객체를 리턴하고, Path로 끝나는 메소드들은 전체 경로를 String으로 리턴한다.

| 메소드                        | 설명                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| String **getAbsolutePath**()  | 절대 경로를 String으로 리턴한다. (현재 프로그램을 실행한 경로 포함) |
| File **getAbsoluteFile**()    | 절대 경로를 File 객체로 리턴한다.                            |
| String **getCanonicalPath**() | 상대 경로를 String으로 리턴한다. (현재 프로그램을 실행한 경로 포함) |
| File **getCanonicalFile**()   | 상대 경로를 File 객체로 리턴한다.                            |
| String **getName**()          | 파일 객체일 경우 파일 이름을, 경로 객체일 경우 전체 경로를 리턴한다. |
| String **getPath**()          | File 객체를 생성할 때 넣어준 경로를 그대로 리턴한다.         |
| String **getParent**()        | 파일 이름을 제외한 경로만을 리턴한다.                        |

```java
File file = new File("../../workspace");
System.out.println("getPath : " + file.getPath());
System.out.println("getAbsolutePath : " + file.getAbsolutePath());
System.out.println("getCanonicalPath : " + file.getCanonicalPath());
```

출력결과

```
getPath : ..\..\workspace
getAbsolutePath : D:\workspace\Test\..\..workspace
getCanonicalPath : D:\workspace
```



## 목록을 살펴보기 위한 메소드

| 메소드                                      | 설명                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| static File[] **listRoots**()               | JVM이 수행되는 OS에서 사용중인 파일 시스템의 루트(root) 디렉터리 목록을 File 배열로 리턴한다. static 메소드이므로, File 객체를 별도로 생성할 필요는 없다. |
| String[] **list**()                         | 현재 디렉터리의 하위에 있는 목록을 String 배열로 리턴한다.   |
| String[] **list**(FilenameFIlter filter)    | 현재 디렉터리의 하위에 있는 목록 중, 매개 변수로 넘어온 filter의 조건에 맞는 목록을 String 배열로 리턴한다. |
| File[] **listFiles**()                      | 현재 디렉터리의 하위에 있는 목록 File 배열로 리턴한다.       |
| File[] **listFiles**(FileFilter filter)     | 현재 디렉터리의 하위에 있는 목록 중 매개 변수로 넘어온 filter의 조건에 맞는 목록을 File 배열로 리턴한다. |
| File[] **listFiles**(FilenameFilter filter) | 현재 디렉터리의 하위에 있는 목록 중 매개 변수로 넘어온 filter의 조건에 맞는 목록을 File 배열로 리턴한다. |



## 출처

- [qna.programmers.co.kr](https://qna.programmers.co.kr/questions/296/%EC%9E%90%EB%B0%94%EC%97%90%EC%84%9C-getpath-getabsolutepath-getcanonicalpath%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%B4-%EB%AD%94%EA%B0%80%EC%9A%94)
