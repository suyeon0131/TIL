# 싱글톤 컨테이너
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [웹 애플리케이션과 싱글톤](#웹-애플리케이션과-싱글톤)
2. [싱글톤 패턴](#싱글톤-패턴)
3. [싱글톤 컨테이너](#싱글톤-컨테이너)
4. [싱글톤 방식의 주의점](#싱글톤-방식의-주의점)
5. [@Configuration과 싱글톤](#configuration과-싱글톤)
6. [@Configuration과 바이트코드 조작의 마법](#configuration과-바이트코드-조작의-마법)

## 웹 애플리케이션과 싱글톤
![Image](https://github.com/user-attachments/assets/ecf56bbf-c92d-4678-95a9-35f5866c4f36)   
- 웹 애플리케이션은 보통 여러 고객이 동시에 요청을 한다.

- **`SingletonTest`** 참고
- 스프링 없는 순수한 DI 컨테이너인 AppConfig는 **요청을 할 때마다 객체를 새로 생성한다.**
- 고객 트래픽이 초당 100이 나오면 초당 100개의 객체가 생성되고 소멸된다 -> 메모리 낭비!
  - 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다 -> **싱글톤 패턴**

## 싱글톤 패턴
- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장한다.
  - 즉, 2개 이상 생성하지 못하도록 막아야 한다. (private 생성자를 사용해서 외부에서 new 사용을 막음)
```java
public class SingletonService {

    // 1. static 영역에 객체를 딱 1개만 생성해둔다.
    private static final SingletonService instance = new SingletonService();

    // 2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
    public static SingletonService getInstance() {
        return instance;
    }

    // 3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막는다.
    private SingletonService() {
    }
}
```

- **`SingletonTest`** 참고
![Image](https://github.com/user-attachments/assets/bf0f33d6-4c4d-4487-bf8e-a78e311b98f6)
- 같은 객체 인스턴스를 반환한다!

- **싱글톤 패턴의 문제점**
  - 구현 코드가 많다.
  - 의존관계상 클라이언트가 구체 클래스에 의존한다. -> DIP 위반
    - OCP 또한 위반 가능성
  - 내부 속성을 변경, 초기화하기 어렵다.
  - private이라 자식 클래스를 만들기 어렵다.
  - 유연성 ㅜㅜ

## 싱글톤 컨테이너
- 스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서 객체 인스턴스를 싱글톤으로 관리한다.
- 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다.
    - 싱글톤 레지스트리: 싱글톤 객체를 생성하고 관리하는 기능
    - 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다. 
    - DIP, OCP, 테스트, private 생성자로부터 자유롭게 싱글톤을 사용할 수 있다.
- **`SingletonTest`** 참고

**싱글톤 컨테이너 적용 후**
![Image](https://github.com/user-attachments/assets/cb80c15b-f7a3-41a5-95be-09f7852c681a)   

## 싱글톤 방식의 주의점
- 상태를 유지하지 않고 무상태(stateless)로 설계해야 한다.
  - 특정 클라이언트에 의존적인 필드가 있으면 안된다.
  - **특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.**
  - 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.

**상태를 유지할 경우 발생하는 문제점 예시**
```java
public class StatefulService {

    private int price; // 상태를 유지하는 필드

    public void order(String name, int price) {
        System.out.println("name = " + name + " price = " + price);
        this.price = price; // 여기가 문제
    }
}

```
```java
class StatefulServiceTest {

    @Test
    void statefulServiceSingleton() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        StatefulService statefulService1 = ac.getBean(StatefulService.class);
        StatefulService statefulService2 = ac.getBean(StatefulService.class);

        // ThreadA: A사용자 10000원 주문
        statefulService1.order("userA", 10000);
        // ThreadB: B사용자 20000원 주문
        statefulService2.order("userB", 20000);

        // ThreadA: 사용자A 주문 금액 조회
        int price = statefulService1.getPrice();
        System.out.println("price = " + price); // 20000

        Assertions.assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }
}
```
- `StatefulService`의 `price` 필드는 공유되는 필드인데, 특정 클라이언트가 값을 변경한다.
- 10000원이 나와야 하는데 20000원이 나온다.

## @Configuration과 싱글톤
- `AppConfig`를 봐보자
  - @Bean memberService -> new MemoryMemberRepository()
  - @Bean orderService -> new MemoryMemberRepository()
  - 싱글톤 위반하는 거 아니야?? -> test 해보자
- **`ConfigurationSingletodTest`** 참고
  - 분명 다른 객체인 줄 알았는데 모두 같은 인스턴스가 공유되어 사용된다. -> 머지?
- AppConfig에 따르면 `memberRepository`가 3번은 호출되어야 하는데, 실제로는 1번만 호출된다.
  - 스프링은 어떻게든 싱글톤을 보장해주는군...

## @Configuration과 바이트코드 조작의 마법
- **`configurationDeep`** 참고
- 순수한 클래스라면 `class hello.core.AppConfig`라고 출력되어야 하는데, 뒤에 xxxCGLIB이 붙어있다.
  - CGLIB: 바이트 코드 조작 라이브러리
- @Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다. -> 싱글톤 보장
- `@Configuration`을 적용하지 않으면, 순수한 클래스가 되며 예상대로 3번이 호출 된다.