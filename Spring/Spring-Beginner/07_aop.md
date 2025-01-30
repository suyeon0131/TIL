# AOP
> 본 게시물은 김영한님의 [스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [AOP가 필요한 상황](#aop가-필요한-상황)
2. [AOP 적용](#aop-적용)

## AOP가 필요한 상황
- 모든 메소드의 호출 시간을 측정하고 싶다면?
  - 메소드가 1000개라면...
- 공통 관심 사항 vs 핵심 관심 사항

![Image](https://github.com/user-attachments/assets/a156d8d0-e8ce-4914-b769-1412e047c6db)

```java
// 회원가입
long start = System.currentTimeMillis();

try {
    validateDuplicateMember(member); // 중복 회원 검증
    memberRepository.save(member);
    return member.getId();
} finally {
    long finish = System.currentTimeMillis();
    long timeMs = finish - start;
    System.out.println("join = " + timeMs + "ms");
}
```
- 이런 식으로 모든 메소드에 try문을 사용해서 시간을 측정해줘야 할까? 회원 가입의 핵심 코드는 try 안의 3줄 뿐인데...
  - 회원 가입, 회원 조회의 시간을 측정하는 기능은 핵심 관심 사항이 아니다. -> `공통 관심 사항`이다.
- 시간을 측정하는 로직과 핵심 비즈니스의 로직이 섞여서 유지보수가 어렵다.
- 시간을 측정하는 로직을 별도의 공통 로직으로 만들기 매우 어렵고 변경할 때도 일일히 작업해야 한다.

-> **이것이 AOP가 필요한 이유이다~**

## AOP 적용
- **AOP: Aspect Oriented Programming***
- 공통 관심 사항과 핵심 관심 사항 분리

![Image](https://github.com/user-attachments/assets/9421bc6c-4509-4742-8259-b1a07649fb79)

**시간 측정 AOP 등록**
```java
// Aop/TimeTraceAop
@Aspect
@Component
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("START: " + joinPoint.toString());
        try {
            return joinPoint.proceed();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
        }
    }
}
```
- `@Component` 스캔 대신에 직접 스프링 빈에 등록해도 됨

![Image](https://github.com/user-attachments/assets/8b727a22-b0ce-4493-9074-540e03be228a)

- 시간을 측정하는 로직을 별도의 공통 로직으로 만들었다.
- 핵심 관심 사항을 깔끔하게 유지할 수 있다.
- 변경이 필요할 때 이 로직만 수정하면 된다.
- 원하는 적용 대상을 선택할 수 있다.

### 스프링의 AOP 동작 방식 설명
**AOP 적용 전 의존 관계**   
![Image](https://github.com/user-attachments/assets/d25704ae-9712-4f5a-8209-2e3cb087f3f4)

**AOP 적용 후 의존 관계**   
![Image](https://github.com/user-attachments/assets/36a7ab0b-f8fb-4fa3-b3fc-6f1bc50ca854)
- **프록시**라는 `가짜 memberService`를 만든다.

**AOP 적용 전 전체 그림**   
![Image](https://github.com/user-attachments/assets/91054d76-0405-444f-81c6-6302cdf58487)

**AOP 적용 후 전체 그림**   
![Image](https://github.com/user-attachments/assets/cc9fad8f-0bfe-43a9-8ec5-72e1db5a0ee5)
