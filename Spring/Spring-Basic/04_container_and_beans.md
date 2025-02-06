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


## BeanFactory와 ApplicationContext

## 다양한 설정 형식 지원 - 자바 코드, XML

## 스프링 빈 설정 메타 정보 - BeanDefinition