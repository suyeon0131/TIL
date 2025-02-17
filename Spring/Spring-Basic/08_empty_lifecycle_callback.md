# 빈 생명주기 콜백
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [빈 생명주기 콜백 시작](#빈-생명주기-콜백-시작)
2. [인터페이스 InitializingBean, DisposableBean](#인터페이스-initializingbean-disposablebean)
3. [빈 등록 초기화, 소멸 메서드](#빈-등록-초기화-소멸-메서드)
4. [애노테이션 @PostConstruct, @PreDestroy](#애노테이션-postconstruct-predestroy)

## 빈 생명주기 콜백 시작
- 외부 네트워크에 미리 연결하는 객체를 하나 생성한다고 가정한다.
  - **`NetworkClient`**, **`BeanLifeCycleTest`** 참고
- 스프링 빈은 객체를 생성하고 의존관계 주입이 다 끝난 다음에, 필요한 데이터를 사용할 수 있는 준비가 완료된다.
  - 의존관계 주입이 완료되면 스프링 빈에게 **콜백 메서드**를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다.
  - 또한, 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다.
- **스프링 빈의 이벤트 라이프사이클**
  - 스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료
- **객체의 생성과 초기화를 분리하자**

스프링은 아래의 방법으로 빈 생명주기 콜백을 지원한다.

## 인터페이스 InitializingBean, DisposableBean
```java
public class NetworkClient implements InitializingBean, DisposableBean
```
- `InitializingBean`은 `afterPropertiesSet()` 메서드로 초기화를 지원한다.
  - 의존관계 주입이 끝나면 호출된다.
- `DisposableBean`은 `destroy()` 메서드로 소멸을 지원한다.
- 초기화, 소멸 인터페이스 단점
  - 스프링 전용 인터페이스이다.
  - 이름을 변경할 수 없다.
  - 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.

## 빈 등록 초기화, 소멸 메서드
- 설정 정보에 `@Bean(initMethod = "init", destroyMethod = "close")`와 같이 초기화, 소멸 메서드를 지정할 수 있다.
- 특징
  - 메서드 이름을 자유롭게 줄 수 있다.
  - 스프링 빈이 스프링 코드에 의존하지 않는다.
  - 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 있다. -> 코드가 아닌 설정 정보를 사용하기 때문
- @Bean의 destroyMethod는 기본값이 (inferred)(추론)으로 등록되어 있다.
  - 종료 메서드를 추론해서 호출한다.

## 애노테이션 @PostConstruct, @PreDestroy
- 이 방법을 쓰자!
- 특징
  - 가장 권장하는 방법이다.
  - 스프링 기술이 아니라 자바 표준이기 때문에 다른 컨테이너에서도 동작한다.
  - 컴포넌트 스캔과 잘 어울린다.
  - 외부 라이브러리에 적용 못한다 ㅜ
    - 이때는 `@Bean(initMethod = "init", destroyMethod = "close")`을 사용하자.