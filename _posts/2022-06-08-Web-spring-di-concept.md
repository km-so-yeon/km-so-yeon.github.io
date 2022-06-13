
- ​

  ## 의존성 주입(Dependency Injection, DI) 개념

  #### 의존관계

  > A 객체가 B 객체를 사용하고 있는 경우
  > 👉 B가 변하면 A에 영향을 미친다.
  > 👉 A 객체가 B 객체에 의존한다.

  ​

  #### 의존관계 2가지 경우

  1. 일체형

  A에서 직접 객체를 생성하여 B를 갖는다. 

  ```java
  class A {
    private B b;
    
    public A() {
      b = new B();
    }
  }
  ```

  ```java
  A a = new A();
  ```

  ​

  1. 조립형

  A가 직접 객체를 생성하지 않고 외부에서 생성해서, 생성한 것을 세팅한 후 세팅한 객체를 사용한다.

  ```java
  class A {
    private B b;
    
    public A() {
      
    }
    
    public void setB(B b) {
      this.b = b;
    }
  }
  ```

  ```java
  /* Setter Injection */
  B b = new B();
  A a = new A();
  a.setB(b);

  /* Construction Injection */
  B b = new B();
  A a = new A(b);
  ```

  ​

  #### 결론

  1. A 객체가 B 객체를 부품으로 사용한다. 👉 A 객체에 B 객체의 의존성을 주입한다.
  2. 일체형보다 조립형이 결합도가 낮다.  👉 조립형이 더 많이 사용된다.
  3. **스프링이 조립형으로 의존성을 주입해준다.** (객체를 외부에서 선언하고 주입받아 사용)

  ​

  #### 장점

  1. 의존성이 줄어든다.

     의존한다는 것은 의존 대상이 변화할 때 이에 맞게 수정해야 한다. DI로 구현하게 되면 주입받는 대상이 변하더라도 그 자체를 수정할 일이 없어가 줄어든다.

  2. 코드의 재활용성이 높아진다.
     A 객체 내부에서만 사용되었던 B 객체를 별도로 구분하여 구분하면 다른 클래스에서 재사용할 수 있다.

  3. 테스트하기 좋은 코드가 된다.
        A 객체 테스트와 B 객체 테스트를 분리하여 진행할 수 있다.

  4. 가독성이 높아진다.
     A 객체와 B 객체의 기능들을 별도로 분리하게 되어 가독성이 높아진다.

  ​

  ## Spring에서의 의존성 주입

  - 외부에서 인스턴스를 만들어서 각 클래스에 주입한다.

  - 인스턴스를 저장하는 공간을 Container라고 부른다.

  - 객체에 대한 제어 권한은 Container에 있다. 이것을 IoC(Inversion of Controller, 제어의 역전)라고 한다.
    👉 인스턴스를 저장하는 Container을 IoC Container (혹은 DI Container)라고 부른다.


  ​

  ### 의존성 주입 사용법

  Spring IoC 컨테이너에 Bean을 등록하고, 생성된 Bean 객체를 주입한다.

  #### 빈(Bean)

  - Spring IoC Container가 관리하는 자바 객체
  - ApplicationContext가 알고있는 객체 (ApplicationContext.getBean()으로 얻어질 수 있는 객체)

  ​

  [Bean 생성원리와 의존성 주입 사용법](https://km-so-yeon.github.io/posts/Web-spring-di-way/)

  ​

  ​

  ### 출처📎

  - [뉴렉처](https://www.youtube.com/watch?v=WjsDN_aFfyw)
  - [망나니개발자](https://mangkyu.tistory.com/150)
  - [Knowledge Repository](https://atoz-develop.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88Bean%EC%9D%98-%EA%B0%9C%EB%85%90%EA%B3%BC-%EC%83%9D%EC%84%B1-%EC%9B%90%EB%A6%AC)

