# 빈 스코프
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [빈 스코프란?](#빈-스코프란)
2. [프로토타입 스코프](#프로토타입-스코프)
3. [프로토타입 스코프 - 싱글톤 빈과 함께 사용 시 문제점](#프로토타입-스코프---싱글톤-빈과-함께-사용-시-문제점)
4. [프로토타입 스코프 - 싱글톤 빈과 함께 사용 시 Provider로 문제 해결](#프로토타입-스코프---싱글톤-빈과-함께-사용-시-provider로-문제-해결)
5. [웹 스코프](#웹-스코프)
6. [request 스코프 예제 만들기](#request-스코프-예제-만들기)
7. [스코프와 Provider](#스코프와-provider)
8. [스코프와 프록시](#스코프와-프록시)

## 빈 스코프란?
- **스코프**: 빈이 존재할 수 있는 범위
- 다양한 스코프
  - **싱글톤**: 기본 스코프. 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프
  - **프로토타입**: 스프링 컨테이너가 프르토타입 빈의 생성과 의존관계 주입까지만 관여하는 매우 짧은 범위의 스코프
  - **웹 관련 스코프**
    - **request**: HTTP 요청 하나가 들어오고 나갈 때까지 유지되는 스코프
    - **session**: 웹 세션이 생성되고 종료될 때까지 유지되는 스코프
    - **application**: 웹의 서블릿 컨텍스트(ServletContext)와 같은 범위로 유지되는 스코프
    - **websocket**: 웹 소켓과 동일한 생명주기를 가지는 스코프

## 프로토타입 스코프
- 싱글톤 스코프과 다르게 프로토타입 스코프를 스프링 컨테이너에 조회하면 컨테이너는 항상 새로운 인스턴스를 생성해서 반환한다.   
![Image](https://github.com/user-attachments/assets/d1a28c40-0dad-4245-a180-6bfb659d24d2)   
- 요청하는 시점에 빈 생성
- 여기서 핵심은, **스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리한다는 것이다.**
  - 프로토타입 빈을 관리할 책임은 빈을 받은 클라이언트에 있다.
  - @PreDestroy 같은 종료 메서드가 호출되지 않는다.
- `singletonTest`, **`prototypeTest`** 참고

```java
find prototypeBean1
PrototypeBean.init
find prototypeBean2
PrototypeBean.init
prototypeBean1 = hello.core.scope.PrototypeTest$PrototypeBean@28f8e165
prototypeBean2 = hello.core.scope.PrototypeTest$PrototypeBean@545f80bf
```
- 생성, 의존관계 주입, 초기화까지만 관여하기 때문에 종료 메서드가 실행되지 않는다.

## 프로토타입 스코프 - 싱글톤 빈과 함께 사용 시 문제점
![Image](https://github.com/user-attachments/assets/128ea48f-a83f-4874-83c2-6fefc7d878f8)   
![Image](https://github.com/user-attachments/assets/50380993-08e3-4a9c-aab5-4019109e78b9)   
- A는 `clientBean`을 스프링 컨테이너에 요청해서 받는다.
- `clientBean.logic()`을 호출한다.
- count 값이 1이 된다.
- B는 `clientBean`을 요청해서 받는다. 싱글톤이므로 항상 같은 것이 반환된다.
  - **clientBean이 내부에 가지고 있는 프로토타입 빈은 이미 과거에 주입이 끝난 빈이다. 주입 시점에 스프링 컨테이너에 요청해서 프로토타입 빈이 새로 생성된 것이지 사용할 때마다 새로 생성되는 것이 아니다.**
- `clientBean.logic()`을 호출한다.
- count 값이 2가 된다.

## 프로토타입 스코프 - 싱글톤 빈과 함께 사용 시 Provider로 문제 해결
### 스프링 컨테이너에 요청
- 싱글톤 빈이 프로토타입을 사용할 때마다 스프링 컨테이너에 새로 요청하는 것이다.
```java
@Autowired
private ApplicationContext ac;

public int logic() {
    PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);
    ...
}
```
- `ac.getBean()`을 통해서 항상 새로운 프로토타입 빈이 생성된다.
- 의존관계를 외부에서 주입(DI) 받는 게 아니라 직접 필요한 의존관계를 찾는다. -> **Dependency Lookup(DL)** - 의존관계 조회(탐색)
- but, 이렇게 애플리케이션 컨텍스트 전체를 주입받게 되면, 스프링 컨테이너에 종속적인 코드가 되고 단위 테스트도 어려워진다.

### ObjectFactory, ObjectProvider
- `ObjectProvider`가 DL 서비스를 제공한다!
  - `ObjectFactory` + 편의기능  
```java
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
- `prototypeBeanProvider.getObject()`를 통해서 항상 새로운 프로토타입 빈이 생성된다.
- `ObjectProvider`의 `getObject()`를 호출하면 내부에서 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. -> DL
- ObjectFactory: 기능 단순. 별도의 라이브러리 필요 없음. 스프링에 의존
- ObjectProvider: ObjectFactory 상속. 옵션, 스트림 처리 등 편의 기능 많음

### JSR-330 Provider
- `jakarta.inject.Provider`
```java
@Autowired
private Provider<PrototypeBean> provider;

public int logic() {
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
- `provider.get()`을 통해서 항상 새로운 프로토타입 빈이 생성된다.
- `provider`의 `get()`을 호출하면 내부에서 스프링 컨테이너를 통해 해당 빈을 찾아서 반환한다. -> DL
-  기능 단순. 별도의 라이브러리 필요
-  자바 표준이므로 다른 컨테이너에서도 사용 가능하다.

## 웹 스코프
- 웹 환경에서만 동작한다.
- 프로토타입과 다르게 스코프의 종료 시점까지 관리한다. -> 즉, 종료 메서드가 호출된다.

- **HTTP request 요청 당 각각 할당되는 request 스코프**   
![Image](https://github.com/user-attachments/assets/ab3f380d-8817-4456-b9d3-d10f791b1e01)   

## request 스코프 예제 만들기
- `spring-boot-starter-web` 라이브러리를 추가하면 스프링 부트는 내장 톰켓 서버를 활용해서 웹 서버와 스프링을 함께 실행시킨다.
- 동시에 여러 HTTP 요청이 들어오면 정확히 어떤 요청이 남긴 로그인지 구분하기 어렵다. -> request 스코프 사용하자~
- UUID를 사용해서 HTTP 요청을 구분하자.
- **`MyLogger`** 참고
  - `@Scope(value = "request")`
  - 빈이 생성되는 시점에 자동으로 `@PostConstruct` 초기화 메서드를 사용해서 uuid를 생성해 저장한다.
  - 빈이 소멸되는 시점에 `@PreDestroy`를 사용해서 종료 메시지를 남긴다.
  - `requestURL`은 빈이 생성되는 시점에는 알 수 없으므로, 외부에서 setter로 입력 받는다.
- `LogDemoController`, `LogDemoService` 참고
  - HttpServletRequest를 통해서 요청 URL을 받는다.
    - 받은 requestURL 값을 myLogger에 저장한다. myLogger는 HTTP 요청 당 각각 구분되므로 다른 HTTP 요청 때문에 값이 섞이는 걱정은 노노
  - **but, 오류 발생**
    - 스프링 애플리케이션을 실행하는 시점에 싱글톤 빈은 생성 및 주입이 가능하지만, request 스코프 빈은 고객의 요청이 와야 생성되기 때문에 아직 생성되지 않는다.

## 스코프와 Provider
- 위와 같은 문제를 Provider로 해결~
- `http://localhost:8080/log-demo`   
![Image](https://github.com/user-attachments/assets/daff931f-917b-4397-81a1-a32c76037289)   
- `ObjectProvider` 덕분에 `ObjectProvider.getObject()`를 호출하는 시점까지 request scope **빈의 생성(요청)을 지연**할 수 있다.
- 같은 HTTP 요청이면 같은 스프링 빈이 반환된다.

## 스코프와 프록시
```java
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
```
- 적용 대상이 클래스이면 `TARGET_CLASS`
- 적용 대상이 인터페이스이면 `INTERFACES`
- MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입해 둘 수 있다.

### 웹 스코프와 프록시 동작 원리
```java
myLogger = class hello.core.common.MyLogger$$SpringCGLIB$$0
```
- CGLIB 라이브러리로 클래스를 상속 받은 가짜 프록시 객체를 만들어서 주입한다.
  - 의존관계 주입 또한 가짜 프록시 객체가 주입된다.
- getClass()로 찍어보면 순수한 MyLogger 클래스가 아님을 알 수 있다.   
![Image](https://github.com/user-attachments/assets/cb636b03-da78-490e-a61b-1955a50614a0)   
- **가짜 프록시 객체는 요청이 오면 그때 내부에서 진짜 빈을 요청하는 위임 로직이 들어있다.**
  - 클라이언트가 `myLogger.log()` 호출 -> 가짜 객체의 메서드 호출
  - 가짜 프록시 객체는 request 스코프의 진짜 `myLogger.log()`를 호출
- 정리
  - 프록시 객체 덕분에 클라이언트는 싱글톤 빈을 사용하는 것처럼 편리하게 request scope를 사용할 수 있다.
  - Provider든, 프록시든 **진짜 객체 조회를 꼭 필요한 시점까지 지연처리** 한다는 것이다.
  - 애노테이션 설정 변경 만으로 원본 객체를 프록시 객체로 대체할 수 있다. -> 다형성, DI 컨테이너의 강점
