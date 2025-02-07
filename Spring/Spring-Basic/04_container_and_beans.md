# 스프링 컨테이너와 스프링 빈
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [스프링 컨테이너 생성](#스프링-컨테이너-생성)
2. [컨테이너에 등록된 모든 빈 조회](#컨테이너에-등록된-모든-빈-조회)
3. [스프링 빈 조회 - 기본](#스프링-빈-조회---기본)
4. [스프링 빈 조회 - 동일한 타입이 둘 이상](#스프링-빈-조회---동일한-타입이-둘-이상)
5. [스프링 빈 조회 - 상속 관계](#스프링-빈-조회---상속-관계)
6. [BeanFactory와 ApplicationContext](#beanfactory와-applicationcontext)
7. [다양한 설정 형식 지원 - 자바 코드, XML](#다양한-설정-형식-지원---자바-코드-xml)
8. [스프링 빈 설정 메타 정보 - BeanDefinition](#스프링-빈-설정-메타-정보---beandefinition)

## 스프링 컨테이너 생성
```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```
- `ApplicationContext`: 스프링 컨테이너, 인터페이스
- `new AnnotationConfigApplicationContext(AppConfig.class)`: 인터페이스의 구현체

### 스프링 컨테이너의 생성 과정
1. 스프링 컨테이너 생성
![Image](https://github.com/user-attachments/assets/90b04456-70c4-4230-a50e-7b606c96758a)   
- 스프링 컨테이너를 생성할 때는 구성 정보를 지정해주어야 한다.
  - 여기서는 `AppConfig.class`

2. 스프링 빈 등록
![Image](https://github.com/user-attachments/assets/0eedbdbc-18d9-4645-b33d-1d392adebd8e)   
- 빈 이름 직접 부여 가능 (`@Bean(name="memberService2")`)

3. 스프링 빈 의존관계 설정
![Image](https://github.com/user-attachments/assets/d725c937-869e-4a5f-ae69-5cdbd9dee28c)   
- 설정 정보를 참고해서 의존관계 주입(DI)

## 컨테이너에 등록된 모든 빈 조회
- 모든 빈 출력하기
  - `ac.getBeanDefinitionNames()`: 스프링에 등록된 모든 빈 이름을 조회한다.
- 애플리케이션 빈 출력하기
  - `getRole()`로 구분한다.
    - `ROLE_APPLICATION`: 일반적으로 사용자가 정의한 빈
    - `ROLE_INFRASTRUCTURE`: 스프링이 내부에서 사용하는 빈

`ctrl + d`: 복제

## 스프링 빈 조회 - 기본
- `ac.getBean(빈이름, 타입)` or `ac.getBean(타입)`
- 조회 대상이 없으면 예외 발생
  - `NosuchBeanDefinitionException: ~~`
- **`ApplicationContextBasicFindTest`** 참고

## 스프링 빈 조회 - 동일한 타입이 둘 이상
- 빈 이름을 지정하자
- `ac.getBeansOfType()`: 해당 타입의 모든 빈을 조회한다.
- **`ApplicationContextSameBeanFindTest`** 참고

## 스프링 빈 조회 - 상속 관계
- 부모 타입으로 조회하면 자식 타입도 함께 조회한다.
  - 가장 위인 `Object` 타입으로 조회하면 모든 스프링 빈이 조회된다.
- **`ApplicationContextExtendsFindTest`** 참고

## BeanFactory와 ApplicationContext
![Image](https://github.com/user-attachments/assets/1809eb0b-c91f-4e4c-ba66-b68102fb5791)   

### BeanFactory  
- 스프링 컨테이너의 최상위 인터페이스이다.
- 스프링 빈을 관리하고 조회하는 역할을 한다.
- ex. `getBean()`

### ApplicationContext
- BeanFactory 기능을 모두 상속 받아서 제공한다.
- 애플리케이션을 개발할 때는 빈을 관리하고 조회하는 기능은 물론이고, 수 많은 부가기능이 필요하다.

### ApplicationContext가 제공하는 부가기능
- **메시지 소스를 활용한 국제화 기능**
  - ex. 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력한다.
- **환경 변수**
  - 로컬, 개발, 운영 등을 구분해서 처리한다.
- **애플리케이션 이벤트**
  - 이벤트를 발행하고 구독하는 모델을 편리하게 지원한다.
- **편리한 리소스 조회**
  - 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회한다.

## 다양한 설정 형식 지원 - 자바 코드, XML
- 스프링 컨테이너는 다양한 형식의 설정 정보를 받아들일 수 있게 유연하게 설계되어 있다.

- 애노테이션 기반 자바 코드 설정 사용
  - `new AnnotationConfigApplicationContext(AppConfig.class)`
- XML 설정 사용
  - `GenericXmlApplicationContext`를 사용하면서 `xml` 설정 파일을 넘긴다.

## 스프링 빈 설정 메타 정보 - BeanDefinition
- 스프링은 어떻게 이런 다양한 설정 형식들을 지원할까? -> 중심에 `BeanDefinition`이라는 추상화 존재!
- `BeanDefinition`: 빈 설정 메타 정보
  - `@Bean`, `<bean>` 당 각각 하나씩 메타 정보가 생성된다.
- 스프링 컨테이너는 이 메타 정보를 기반으로 스프링 빈을 생성한다.

![Image](https://github.com/user-attachments/assets/520ea9a2-69ed-4a98-be80-63762d267854)

- **BeanDefinition 정보**
  - BeanClassName: 생성할 빈의 클래스 명
  - factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름 (ex. appConfig)
  - factoryMethodName: 빈을 생성할 팩토리 메서드 지정 (ex. memberService)
  - Scope: 싱글톤 (기본값)
  - lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한 생성을 지연 처리 하는지 여부
  - InitMethodName: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명
  - DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
  - Constructor arguments, Properties: 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음)