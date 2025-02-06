# 스프링 핵심 원리 이해2 - 객체 지향 원리 적용
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [새로운 할인 정책 개발](#새로운-할인-정책-개발)
2. [새로운 할인 정책 적용과 문제점](#새로운-할인-정책-적용과-문제점)
3. [관심사의 분리](#관심사의-분리)
4. [AppConfig 리팩터링](#appconfig-리팩터링)
5. [새로운 구조와 할인 정책 적용](#새로운-구조와-할인-정책-적용)
6. [좋은 객체 지향 설계의 5가지 원칙의 적용](#좋은-객체-지향-설계의-5가지-원칙의-적용)
7. [IoC, DI, 그리고 컨테이너](#ioc-di-그리고-컨테이너)
8. [스프링으로 전환하기](#스프링으로-전환하기)

## 새로운 할인 정책 개발
- 정액 할인 정책을 정률(%) 할인 정책으로 변경하자
  - `RateDiscountPolicy`

`ctrl + shfit + T`: Test 클래스 생성   
`Assertions`는 import static 해주는 게 좋다.

## 새로운 할인 정책 적용과 문제점
- 할인 정책을 변경하려면 클라이언트인 `OrderServiceImpl` 코드를 수정해야 한다.
```java
//private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
```
- 역할과 구현을 분리했다. -> **O**
- 다형성을 활용하고, 인터페이스와 구현 객체를 분리했다. -> **O**
- OCP, DIP 같은 객체지향 설계 원칙을 준수했다. -> O 처럼 보이지만 **사실은 아니다** 
  - DIP: 인터페이스 뿐만 아니라 구현 클래스에도 의존하고 있다.
  - OCP: 기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다.

**기대와는 다른 실제 의존관계**
![Image](https://github.com/user-attachments/assets/981a4d51-62b0-4336-a300-1acd4b6bc1c1)   
- `OrderServiceImpl`가 `DiscountPolicy` 인터페이스 뿐만 아니라 `FixDiscountPolicy` 구현체도 의존하고 있다. -> **DIP 위반!**
- 그림에서 OrderServiceImpl과 FixDiscountPolicy를 연결하는 실선이 없어야 기대했던 의존 관계가 된다. 즉, `OrderServicePolicy` 코드를 수정해야 한다. -> **OCP 위반!**

### 어떻게 문제를 해결할 수 있을까?
- 인터페이스에만 의존하도록 의존관계를 변경하면 된다.
```java
public class OrderServiceImpl implements OrderService {
    //private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
    private DiscountPolicy discountPolicy;
}
```
- 구현체가 없는데? -> 실제로 실행하면 NullPointerException 발생
  - **누군가가 `OrderServiceImpl`에 `DiscountPolicy`의 구현 객체를 대신 생성하고 주입해줘야 한다.**

## 관심사의 분리
### AppConfig 등장
- 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, **구현 객체를 생성**하고, **연결**하는 책임을 가지는 별도의 설정 클래스를 만들자

- 구현 객체 생성
  - `MemberServiceImpl`, `MemoryMemberRepository`, `OrderServiceImpl`, `FixDiscountPolicy`
- 생성자를 통해서 주입(연결)
  - `MemberServiceImpl` -> `MemoryMemberRepository`
  - `OrderServiceImpl` -> `MemoryMemberRepository`, `FixDiscountPolicy`

- 이제 `MemberServiceImpl`은 `MemoryMemberRepository`를 의존하지 않는다.
  - 단지 `MemberRepository` 인터페이스만 의존한다.
- `MemberServiceImpl` 입장에서는 생성자를 통해 어떤 구현 객체가 들어올지 알 수 없다.
- `MemberServiceImpl`의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(`AppConfig`)에서 결정된다.
- `MemberServiceImpl`은 이제부터 **의존관계에 대한 고민은 외부**에 맡기고 **실행에만 집중**하면 된다.

**회원 객체 인스턴스 다이어그램**
![Image](https://github.com/user-attachments/assets/c793dfe0-57c2-4d97-9405-ea7eccb272b4)   
- `appConfig` 객체는 `memoryMemberRepository` 객체를 생성하고 그 참조값으로 `memberServiceImpl`을 생성하면서 생성자로 전달한다.

**OrderServiceImpl - 생성자 주입**
- 이제 `OrderServiceImpl`은 `FixDiscountPolicy`를 의존하지 않는다.
  - 단지 `DiscountPolicy` 인터페이스만 의존한다.
- (위 내용과 동일)

### AppConfig 실행
`ctrl + e`: 히스토리   
```java
public class MemberServiceTest {

    MemberService memberService;

    @BeforeEach
    public void beforeEach() {
        AppConfig appConfig = new AppConfig();
        memberService = appConfig.memberService();
    }
}
```
- `AppConfig` = 공연 기획자

## AppConfig 리팩터링
- 역할이 잘 보이게 구현해야한다.
```java
public class AppConfig {

    public MemberService memberService() {
        // 생성자 주입
        return new MemberServiceImpl(memberRepository());
    }

    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy() {
        return new FixDiscountPolicy();
    }
}
```

## 새로운 구조와 할인 정책 적용
- 정률 할인 정책으로 변경해보자
![Image](https://github.com/user-attachments/assets/c2e4d673-2db2-4986-ad80-b9b56618f71f)
- `AppConfig`의 `FixDiscountPolicy`를 `RateDiscountPolicy`로만 변경하면 된다 ㄷㄷ
  - 사용 영역의 어떤 코드도 변경할 필요가 없다.

## 좋은 객체 지향 설계의 5가지 원칙의 적용 (SOLID)
**SRP (단일 책임 원칙)**
- 구현 객체를 생성하고 연결하는 책임 -> `AppConfig`
- 실행하는 책임 -> 클라이언트 객체

**DIP (의존관계 역전 원칙)**
- 클라이언트 코드가 추상화 인터페이스에만 의존하도록 코드를 변경했다.
- `AppConfig`가 객체 인스턴스를 클라이언트 코드 대신에 생성해서 클라이언트 코드에 의존관계를 주입했다.

**OCP**
- 소프트웨어 요소를 새롭게 확장해도 사용 영역의 변경은 닫혀있다.

## IoC, DI, 그리고 컨테이너
### 제어의 역전 IoC(Inversion of Control)
- 프로그램의 제어 흐름을 직접 제어하지 않고 외부에서 관리하는 것 -> 제어의 역전
- `AppConfig`가 등장한 후, 구현 객체는 자신의 로직을 실행하는 역할만 담당한다.
  - 프로그램의 제어 흐름은 AppConfig가 가져간다.

**프레임워크 vs 라이브러리**
- 내가 작성한 코드를 제어하고, 대신 실행한다. -> **프레임워크**(JUnit)
- 내가 작성한 코드가 직접 제어의 흐름을 담당한다. -> **라이브러리**

### 의존관계 주입 DI
- 의존관계는 **정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계**를 분리해서 생각해야 한다.

**정적인 클래스 의존 관계**
- 정적인 의존관계는 애플리케이션을 실행하지 않아도 분석할 수 있다.
- `OrderServiceImpl`은 `MemberRepository`, `DiscountPolicy`에 의존한다는 것을 알 수 있다.
  - but, 실제로 어떤 객체가 주입될 지는 알 수 없다.

**동적인 객체 인스턴스 의존 관계**
- 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다.
- 애플리케이션 **실행 시점(런타임)**에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결되는 것 -> **의존관계 주입**
- 객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결된다.
- **의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다!**

- AppConfig처럼 객체를 생성하고 관리하면서 의존관계를 연결해주는 것 -> IoC 컨테이너, **DI 컨테이너**

## 스프링으로 전환하기
**`AppConfig` 스프링 기반으로 변경**
- `@Configuration`: AppConfig에 설정을 구성한다.
- `@Bean`: 스프링 컨테이너에 스프링 빈으로 등록한다.

**`MemberApp`, `OrderApp`에 스프링 컨테이너 적용**
- `ApplicationContext`: 스프링 컨테이너
- 스프링 컨테이너는 `@Configuration`이 붙은 `AppConfig`를 설정(구성) 정보로 사용한다.
  - `@Bean`이라고 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다.
  - AppConfig를 사용해 객체를 직접 조회하지 않고, 스프링 컨테이너를 통해 필요한 스프링 빈(객체)를 찾는다. (`applicationContext.getBean()`)

> 스프링 컨테이너를 사용하면 어떤 장점이 있나??   
> **어마어마한 장점 존재!!**