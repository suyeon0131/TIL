# 스프링 핵심 원리 이해1 - 예제 만들기
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [비즈니스 요구사항과 설계](#비즈니스-요구사항과-설계)
2. [회원 도메인 설계](#회원-도메인-설계)
3. [회원 도메인 개발](#회원-도메인-개발)
4. [회원 도메인 실행과 테스트](#회원-도메인-실행과-테스트)
5. [주문과 할인 도메인 설계](#주문과-할인-도메인-설계)
6. [주문과 할인 도메일 개발](#주문과-할인-도메인-개발)
7. [주문과 할인 도메인 실행과 테스트](#주문과-할인-도메인-실행과-테스트)

## 비즈니스 요구사항과 설계
- 회원 가입, 조회
- 등급: 일반, VIP
- 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다. (미확정)
- 상품 주문 가능
- 회원 등급에 따른 할인 정책 적용
  - 할인 정책 변경 가능성 O

- 확실하지 않은 것에 대해서는 구현체를 언제든지 변경할 수 있도록 인터페이스를 설계하면 된다.
- 순수 자바로만 개발한다! 스프링은 나중에 나온당

## 회원 도메인 설계
**회원 도메인 협력 관계**   
![Image](https://github.com/user-attachments/assets/c7da40c8-81fc-4619-82b6-d5ead2b9b711)   
- 회원 데이터 저장 방식이 정해지지 않았기 때문에, 메모리 회원 저장소를 만들어 개발을 진행한다. (메모리는 재부팅하면 사라지기 때문에 오로지 개발용~)

## 회원 도메인 개발
### 회원 엔티티
- enum으로 회원 등급 분류 (BASIC, VIP)
- `Member` 클래스로 id, name, grade

### 회원 저장소
- `MemberRepository` 인터페이스와 이를 구현하는 `MemoryMemberRepository` 구현체
  - 데이터베이스가 확정되지 않았기 때문에 가장 단순한 메모리 회원 저장소로 개발 진행
  - 코드에서, `HashMap`은 동시성 이슈가 발생할 수 있기 때문에 `ConcurrentHashMap`을 사용하자

### 회원 서비스
- `MemberService` 인터페이스와 이를 구현하는 `MemberServiceImpl` 구현체

## 회원 도메인 실행과 테스트
- `soutv`: 변수 선택 가능한 출력문이 작성된다. (sout은 알았는데...우와)
- JUnit 테스트 코드 작성
  - given-when-then

### 회원 도메인 설계의 문제점
**의존관계가 인터페이스 뿐만 아니라 구현까지 모두 의존하는 문제점이 있다.**

```java
public class MemberServiceImpl implements MemberService {
  private final MemberRepository memberRepository = new MemoryMemberRepository();
}
```
- `MemberServiceImpl`는 인터페이스인 **`MemberRepository`**를 의존하지만, 동시에 구현체인 **`MemoryMemberRepository`**도 의존한다. -> **DIP 위반!!**

## 주문과 할인 도메인 설계
**주문 도메인**   
![Image](https://github.com/user-attachments/assets/dd27e29e-f4b7-43cb-a3f5-df1dc88c09f3)   
- **역할과 구현을 분리**했기 때문에, 회원 저장소는 물론이고 할인 정책도 유연하게 변경 가능하다. -> 협력 관계를 그대로 재사용 가능

## 주문과 할인 도메인 개발
- 할인 정책 인터페이스 `DisCountPolicy`
  - 정액 할인 정책 구현체 `FixDiscountPolicy`
- 주문 엔티티 `Order`
- 주문 서비스 인터페이스 `OrderService`
  - 주문 서비스 구현체 `OrderServiceImpl`

```java
public class OrderServiceImpl implements OrderService {

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);
    }
}
```
- `OrderServiceImpl`: 난 할인에 대한 거 몰라 할인은 너가 알아서 해!
- `discountPolicy`: 오키
  - **단일 책임 원칙을 잘 지켰당**

## 주문과 할인 도메인 실행과 테스트