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

- FileFilter, FilenameFilter는 인터페이스이다.
  - 어떤 조건을 주려면 이 인터페이스를 구현해야 한다.
  - 메소드에서 파일이나 경로를 만날 때마다 accept() 메소드가 자동으로 수행된다. 따라서 accept() 메소드를 어떻게 구현했는지에 따라 필요한 파일의 목록이 달라진다.

```java
public class PNGFileFilter implements FileFilter {
    
    @Override
    public boolean accept(File file) {
        if(file.isFile()) {
            String fileName = file.getName();
            if(fileName.endsWith(".png")) return true;
        }
        return false;
    }
}
```

```java
File file = new File(pathName);
File[] mainFileList = file.listFiles(new PNGFileFilter());	// .png로 끝나는 파일들만 포함됨
```



## InputStream과 OutputStream

자바의 I/O는 기본적으로 InputStream과 OutputStream이라는 Abstract 클래스를 통해 제공된다.

- byte를 다루기 위한 것이다.



### InputStream

```java
public abstract class InputStream extends Object implements Closeable { ... 
```

어떤 대상의 데이터를 읽을 때 InputStream의 자식 클래스를 통해서 읽으면 된다.

#### 주요 메소드

- int **read**(byte[] b) : 매개 변수로 넘어온 바이트 배열에 데이터를 담는다. (리턴값은 데이터를 담은 개수)
- void **close**() : 해당 리소스를 닫을 때 사용 (필수!!)



#### InputStream을 확장한 주요 클래스

| 클래스            | 설명                                                         |
| ----------------- | ------------------------------------------------------------ |
| FileInputStream   | 파일을 읽는데 사용한다. 주로 우리가 쉽게 읽을 수 있는 텍스트 파일을 읽기 위한 용도라기보다 이미지와 같이 바이트 코드로 된 데이터를 읽을 때 사용한다. |
| FilterInputStream | 다른 입력 스트림을 포괄하며, 단순히 InputStream 클래스가 Override 되어있다. |
| ObjectInputStream | ObjectOutputStream으로 저장한 데이터를 읽는데 사용한다.      |

- FilterInputStream 생성자는 protected로 선언되어 있어서 상속 받은 클래스에서만 객체를 생성할 수 있다.
  - FilterInputStream을 확장한 클래스 : BufferedInputStream, CheckedInputStream, 등



### OutputStream

```java
public abstract class OutputStream extends Object implements Closeable, Flushable { ...
```

어떤 대상의 데이터를 쓸 때 OutputStream의 자식 클래스를 통해서 쓰면 된다.

- Closeable, Flushable 인터페이스를 구현하였다.

  - Closeable : close() 메소드만 선언되어 있어 이 인터페이스를 구현하면 해당 리소스는 close()를 통해 닫으라는 의미이다.

  - Flushable : flush() 메소드만 선언되어 있다. 어떤 리소스를 저장을 할 때 버퍼를 사용한다면 현재 버퍼에 있는 내용을 기다리지말고 무조건 저장하라는 의미이다.

#### 주요 메소드

- void **write**(byte[] b) : 매개 변수로 받은 바이트 배열을 저장한다.
- void **close**() : 해당 리소스를 닫을 때 사용 (필수!!)



## Reader와 Writer

char 기반의 문자열을 처리하기 위한 클래스이다.

### Reader

```java
public abstract class Reader extends Object implements Readable, Closeable { ... 
```

char 배열의 데이터를 읽을 때 사용한다.

#### 주요 메소드

- int **read**(char[] cbuf) : 매개 변수로 넘어온 char 배열에 데이터를 담는다. (리턴값은 데이터를 담은 개수)
- abstract void **close**() : Reader에서 작업중인 대상을 해제한다. (필수)



### Writer

```java
public abstract class Writer extends Object implements Appendable, Closeable, Flushable { ...
```

char 배열의 데이터를 쓸 때 사용한다.

- Appendable 인터페이스를 구현한다.
  - 각종 문자열을 추가하기 위해 선언되었다. (Java 5부터 추가)

#### 주요 메소드

- void **write**(char[] cbuf) : 매개 변수로 넘어온 char의 배열을 추가한다.
  - void write(String str)
- void **append**(char c) : 매개 변수로 넘어온 char를 추가한다.
  - void append(CharSequense csq) : CharSequence를 구현한 String, StringBuilder, StringBuffer를 받아서 처리할 수 있다.
- abstract void **flush**() : 버퍼에 있는 데이터를 강제로 대상 리소스에 쓰도록 한다.
- abstract void **close**() : 쓰기 위해 열은 스트림을 해제한다.



## 텍스트 파일 쓰기

### 1. FileWriter 클래스 사용

char 기반의 내용을 파일로 쓸 수 있다.

#### 주요 생성자

- FileWriter(File file) : File 객체를 매개 변수로 받아 객체를 생성한다.
- FileWriter(String fileName) : 지정한 문자열의 경로와 파일 이름에 해당하는 객체를 생성한다.
- FileWriter(String fileName, boolean append) : 지정한 문자열의 경로와 파일 이름에 해당하는 객체를 생성한다. append값에 따라 데이터를 추가할지 덮어쓸지 정한다.

### 2. BufferedWriter 클래스 사용

write(), append() 메소드를 사용해서 데이터를 쓰면, 메소드를 호출할 때마다 파일에 쓰기 때문에 매우 비효율적이다. 이러한 단점을 보완하기 위해 BufferWriter 클래스를 사용한다.

#### 생성자

- BufferedWriter(Writer out)
- BufferedWriter(Writer out, int size)

Write 객체를 구현한 BufferWriter를 매개 변수로 받을 수 있다.

```java
public void writeFile(String fileName, int numberCount) {
    FileWriter fileWriter = null;
    BufferedWriter bufferedWriter = null;
    try {
        fileWriter = new FileWriter(fileName);
        bufferedWriter = new BufferedWriter(fileWriter);
        for(int loop = 0; loop <= numberCount; loop++) {
            bufferedWriter.write(Integer.toString(loop));	// 데이터 입력
            bufferedWriter.newLine();	// 줄바꿈
        }
        System.out.println("Write success");
    } catch (IOException ioe) {
        ioe.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if(bufferedWriter != null) {
            try {
                bufferedWriter.close();
            } catch(IOException ioe) {
				ioe.printStackTrace();
            }
        }
        if(fileWriter != null) {
            try {
                fileWriter.close();
            } catch(IOException ioe) {
                ioe.printStackTrace();
            }
        }
    }
}
```

FileWriter 객체를 생성할 때 IOException이 발생하는 경우

- 매개 변수로 넘어온 파일이름이 파일이 아닌 경로를 의미할 경우
- 해당 파일이 존재하지 않지만, 권한 등의 문제로 생성할 수 없는 경우
- 파일이 존재하지만, 여러 이유로 파일을 열 수 없는 경우

FileWriter나 BufferedWriter 변수를 try문 안에서 선언했다면, finally에서 close() 메소드를 호출할 수 없다.

- 반드시 try문장 전에 변수를 선언해야 한다.
- finally에서 close()하는 이유 : try블록에서 구현했다면 중간에 예외가 발생했을 때 close()가 호출되지 않는다. catch 블록에서 구현했다면 일일이 close()를 모두 구현해주어야 한다.

객체 생성 시에는 FileWriter, BufferWriter 순으로, 객체를 닫아줄 때는 BufferedWriter, FileWriter 순으로 닫아준다.



## 텍스트 파일 읽기

FileReader, BufferedReader 클래스 사용

```java
public void readFile(String fileName, int numberCount) {
    FileReader fileReader = null;
    BufferedReader bufferedReader = null;
    try {
        fileReader = new FileReader(fileName);
        bufferedReader = new BufferedReader(fileReader);
        // 읽기 시작
        Strint data;
        while((data=bufferedReader.readLine()) != null) {
            System.out.println(data);
        }
        // 읽기 끝
        System.out.println("Reader success");
    } catch (IOException ioe) {
        ioe.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if(bufferedReader != null) {
            try {
                bufferedReader.close();
            } catch(IOException ioe) {
				ioe.printStackTrace();
            }
        }
        if(fileReader != null) {
            try {
                fileReader.close();
            } catch(IOException ioe) {
                ioe.printStackTrace();
            }
        }
    }
}
```

FileReader 대신 File, 
BufferedReader 대신 Scanner을 사용했을 경우

```java
public void readFileWithScanner(String fileName) {
    File file = new File(fileName);
	Scanner scanner = null;
    try {
        scanner = new Scanner(file);
        // 읽기 시작
        while(scanner.hasNextLine()) {	// 다음 줄이 있는지 확인
            System.out.println(scanner.nextLine());	// 다음 줄의 내용을 한 줄씩 리턴
        }
        // 읽기 끝
        System.out.println("Reader success");
    } catch (IOException ioe) {
        ioe.printStackTrace();
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if(scanner != null) {
            scanner.close();
        }
    }
}
```





## 출처

- 자바의 신

- [qna.programmers.co.kr](https://qna.programmers.co.kr/questions/296/%EC%9E%90%EB%B0%94%EC%97%90%EC%84%9C-getpath-getabsolutepath-getcanonicalpath%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90%EC%9D%B4-%EB%AD%94%EA%B0%80%EC%9A%94)
