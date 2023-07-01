---
title: "[Java] 네트워크 프로그래밍"
author: "김소연"
date: 2023-07-01 18:00:00 -0500
categories: [Language, Java]
tags: [Java]
---



자바로 TCP 통신을 할 경우 자바에서 제공하는 API를 사용하면 된다.




## Socket 클래스

자바에서 TCP 통신을 수행할 때 사용하는 클래스이다.

- 데이터를 보내는 쪽(보통 클라이언트)에서 객체를 생성하여 사용한다.
- 데이터를 받는 쪽(보통 서버)에서 클라이언트 요청을 받으면, 요청에 대한 Socket 객체를 생성하여 처리한다.

👉 원격에 있는 장비와의 연결 상태를 보관하고 있다.



### ServerSocket

서버에서 데이터를 받을 때 사용하는 클래스이다.

ServerSocket 클래스에서 제공하는 메소드에서 클라이언트 요청이 생기면 Socket 객체를 생성해서 전달해준다.



#### 생성자

| 생성자                                                    | 설명                                                         |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| ServerSocket()                                            | 서버 소켓 객체만 생성한다.                                   |
| ServerSocket(int port)                                    | 지정된 포트를 사용하는 서버 소켓을 생성한다.                 |
| ServerSocket(int port, int backlog)                       | 지정된 포트와 backlog 개수를 가지는 소켓을 생성한다.         |
| ServerSocket(int port, int backlog, InetAddress bindAddr) | 지정된 포트와 backlog 개수를 가지는 소켓을 생성하며, bindAddr에 있는 주소에서의 접근 만을 허용한다. |

- `backlog` : 큐의 개수 (ServerSocket 객체가 바빠서 연결 요청을 처리 못하고 대기시킬 때의 최대 대기 개수)
  - 지정해주지 않을 경우 50개로 지정
  - 애플리케이션의 접속이 원할하지 않을 경우 적절하게 증가시켜주는 것이 좋다.
- `InetAddress bindAddr` : 특정 주소에서만 접근이 가능하도록 지정할 때 사용한다.



매개변수가 있는 생성자를 통해 만들어진 클래스들은 객체가 생성되자마자 연결을 대기할 수 있는 상태가 된다.

- 매개변수가 없을 경우 연결작업을 해야 대기가 가능하다.



### 메소드

| 메소드              | 설명                                                         |
| ------------------- | ------------------------------------------------------------ |
| Socket **accpet**() | 새로운 소켓 연결을 기다리고, 연결이 되면 Socket 객체를 리턴한다.<br />(객체 생성 후 사용자의 요청을 대기한다.) |
| void **close**()    | 소켓 연결을 종료한다.                                        |

`close()` 메소드 처리를 하지 않고, JVM이 계속 동작중이라면, 해당 포트는 동작하는 서버나 PC에서 다른 프로그램이 사용할 수 없다.



### Socket 생성자

데이터를 받는 서버에서는 클라이언트에서 접속을 하면 Socket 객체를 생성하지만,
데이터를 보내는 클라이언트에서는 Socket 객체를 직접 생성해야만 한다.

| 생성자                                                       | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Socket()                                                     | 소켓 객체만 생성                                             |
| Socket(**Proxy proxy**)                                      | 프록시 관련 설정과 함께 소켓 객체만 생성                     |
| Socket(**SocketImpl impl**)                                  | 사용자가 지정한 SocketImpl 객체를 사용하여 소켓 객체만 생성  |
| Socket(**InetAddress address, int port**)                    | 소켓 객체 생성 후 address와 port를 사용하는 서버에 연결      |
| Socket(**InetAddress address, int port, InetAddress localAddr, int localPort**) | 소켓 객체 생성 후 address와 port를 사용하는 서버에 연결하고, 지정한 localAddr와 localPort에 접속 |
| Socket(**String host, int port**)                            | 소켓 객체 생성 후 host와 port를 사용하는 서버에 연결         |
| Socket(**String host, int port, InetAddress localAddr, int localPort**) | 소켓 객체 생성 후 host와 port를 사용하는 서버에 연결하며, 지정된 localAddr과 localPort에 접속 |

- Socket(**String host, int port**) 생성자를 사용하는 것이 가장 편하고, 
  다른 생성자들은 용도가 있는 Socket 객체를 생성하는 것이라고 생각하면 된다.
- 네번째 생성자부터는 객체 생성과 함께 지정된 서버에 접속을 한다.
- 소켓 연결에 문제가 있을 경우 끊어주는 Timeout 관련 메소드를 참고하면 된다.



## 소켓 통신

#### 데이터를 받는 서버

소켓을 대기하는 서버

```java
public void startServer() {
    ServerSocket server = null;
    Socket client = null;
    
    try {
        server = new ServerSocket(9999);
        
        while(true) {
            System.out.println("Server:Waiting for request.");
            client = server.accept();
            System.out.println("Server:Accepted.");
            InputStream stream = client.getInputStream();
            
            // 받은 데이터 처리
            BufferedReader in = new BufferedReader(new InputStreamReader(stream));
            String data = null;
            StringBuilder receiveData = new StringBuilder();
            while((data = in.readLine()) != null) {
                receivedData.append(data);
            }
            System.out.println("Received data:"+receivedData);
            in.close();
            
            client.close();
            
            if(receivedData != null && "EXIT".equals(receivedData.toString())) {
                break;
            }
        }
        
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if(server != null) {
            server.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

1. ServerSocket 객체 생성
   포트번호 9999를 이용해서 생성했으므로, 다른 프로그램에서 이 프로그램을 띄운 서버로 접근하려면 9999포트를 사용하면 된다.
2. `accept()` 메소드 호출
   다른 원격 호출을 대기하는 상태가 된다. 연결이 완료되면 Socket 객체를 리턴하여 client 변수에 할당된다.
3. `getInputStream()` 메소드 호출
   InputStream 객체를 받아서 데이터를 받을 수 있게 된다.
   만약 접속한 상대방에게 데이터를 전송하려면 `getOutputStream()` 메소드를 호출하여 OutputSream 객체를 받은 후 이 stream에 전달하면 된다.
4. `client.close()` 메소드 호출
   모든 데이터 처리가 끝난 후 Socket 사용이 종료되었다는 것을 알린다.
5. `server.close()` 메소드 호출
   더 이상 소켓 수신할 필요가 없다는 것을 알린다.



#### 데이터를 전송하는 클라이언트

```java
public void sendSocketData(String data) {
    Socket socket = null;
    try {
		System.out.println("Cleint:Connecting");
        socket = new Socket("127.0.0.1", 9999);
        System.out.println("Cleint:Connect status=" + socket.isConnected());
        Thread.sleep(1000);
        OutputStream stream = socket.getOutputStream();
        
        // 보내는 데이터 처리
        BufferedOutputStream out = new BufferedOutputStream(stream);
        byte[] bytes = data.getBytes();
        out.write(bytes);
		System.out.println("Client:Sent data");
        out.close();
        
    } catch(Exception e) {
        e.printStackTrace();
    } finally {
        if(socket != null) {
            try {
                socket.close();
            } catch(Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

1. Socket 객체 생성
   127.0.0.1이라는 IP와, 포트번호 9999로 객체를 생성하고 접속을 했다.
   127.0.0.1은 같은 장비를 의미하고, 포트번호는 서버쪽에 지정한 포트와 동일해야 한다.
2. `getOutputStream()` 메소드 호출
   OutputStream 객체를 받아서 데이터를 서버에 전달할 수 있게 된다.
3. `socket.close()` 메소드 호출
   데이터를 전달한 후 소켓 연결을 닫는다.



#### 흐름

![gc2](/assets/img/network.jpg){:width="80%" height="80%"}

클라이언트에서 서버로 데이터를 전송했지만, 반대로 데이터를 전송하는 것도 상관 없다. (꼭 단방향 X)



### 발생할 수 있는 예외

- `java.net.BindException`

Address already in use 

서버를 띄어놓고 또 띄웠을 때 발생한다. 
이미 지정된 port 번호를 사용하고 있기 때문에 동일한 port 번호를 사용할 수 없기 때문이다.

- `java.net.ConnectException`

Connection refused

서버를 띄어놓지 않고 클라이언트 프로그램만 수행했을 때 발생한다.
받을 서버가 없으니 던질 곳도 없기 때문이다.



## 출처

자바의 신