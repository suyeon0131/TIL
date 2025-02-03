# 스프링 핵심 원리 이해2 - 객체 지향 원리 적용
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [새로운 할인 정책 개발](#새로운-할인-정책-개발)
2. [새로운 할인 정책 적용과 문제점](#새로운-할인-정책-적용과-문제점)
3. [관심사의 분리](#관심사의-분리)
4. [AppConfig 리팩터링](#appconfig-리팩터링)
5. [새로운 구조와 할인 정책 적용](#새로운-구조와-할인-정책-적용)
6. [전체 흐름 정리](#전체-흐름-정리)
7. [좋은 객체 지향 설계의 5가지 원칙의 적용](#좋은-객체-지향-설계의-5가지-원칙의-적용)
8. [IoC, DI, 그리고 컨테이너](#ioc-di-그리고-컨테이너)
9. [스프링으로 전환하기](#스프링으로-전환하기)

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

## AppConfig 리팩터링

## 새로운 구조와 할인 정책 적용

## 전체 흐름 정리

## 좋은 객체 지향 설계의 5가지 원칙의 적용

## IoC, DI, 그리고 컨테이너

## 스프링으로 전환하기