# 컴포넌트 스캔
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [컴포넌트 스캔과 의존관계 자동 주입 시작하기](#컴포넌트-스캔과-의존관계-자동-주입-시작하기)
2. [탐색 위치와 기본 스캔 대상](#탐색-위치와-기본-스캔-대상)
3. [필터](#필터)
4. [중복 등록과 충돌](#중복-등록과-충돌)

## 컴포넌트 스캔과 의존관계 자동 주입 시작하기
- `shift x 2`: 검색
- 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔 기능을 제공한다.
- **`AutoAppConfig`** 참고
  - `AppConfig`와 다르게 `@Bean`으로 등록한 클래스가 하나도 없다.
  - 클래스에 `@Component` 애노테이션을 붙이면 컴포넌트 스캔의 대상이 된다.
- 컴포넌트 스캔을 사용하면 빈이 자동으로 등록되는데 의존 관계를 설정할 수 없게 된다. -> `@Autowired` 사용
- **`AutoAppConfigTest`** 참고
  - `AnnotationConfigApplicationContext`를 사용하는 것은 동일하나 설정 정보로 `AutoAppConfig` 클래스를 넘긴다.
- **`@ComponentScan`**
  - `@Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다.
    - 기본 이름은 클래스명을 사용하되 맨 앞 글자를 소문자로 사용한다.
    - 직접 지정 -> `@Component("name")`
- **`Autowired`**
  - 생성자에 지정하면, 스프링 컨테이너가 자동으로 스프링 빈을 찾아서 주입한다.
  
## 탐색 위치와 기본 스캔 대상
### 탐색할 패키지의 시작 위치 지정
- `basePackages`: 탐색할 패키지의 시작 위치를 지정한다.
- `basePackageClasses`: 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.
- 권장하는 방법
  - 프로젝트 시작 루트에 `AppConfig` 같은 메인 설정 정보를 투고, `@ComponentScan` 애노테이션으로 붙이고, basePackages 지정은 생략한다.
  - `@SpringBootAplication`을 시작 루트에 두는 것이 관례이다.

### 컴포넌트 스캔 기본 대상
- `@Component`
- `@Controller`
  - 스프링 MVC 컨트롤러에서 사용한다.
- `@Service`
  - 스프링 비즈니스 로직에서 사용한다.
- `@Repository`
  - 스프링 데이터 접근 계층으로 인식하고 데이터 계층의 예외를 스프링 예외로 변환해준다.
- `@Configuration`
  
## 필터
- `includeFilters`: 컴포넌트 스캔 대상을 추가로 지정한다.
- `excludeFilters`: 컴포넌트 스캔에서 제외할 대상을 지정한다.
- `MyIncludeComponent`, `MyExcludeComponent`, `BeanA`, `BeanB`, `ComponentFilterAppConfigTest` 참고
  - `includeFilters`에 `MyIncludeComponent` 애노테이션을 추가해서 BeanA가 스프링 빈에 등록된다.
  - 반대의 이유로 BeanB는 등록되지 않는다.

### FilterType 옵션
- ANNOTATION: 기본값. 애노테이션을 인식해서 동작한다.
- ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작한다.
- ASPECTJ: AspectJ 패턴 사용
- REGEX: 정규 표현식
- CUSTOM: `TypeFilter`라는 인터페이스를 구현해서 처리한다.

## 중복 등록과 충돌
### 자동 빈 등록 vs 자동 빈 등록
- 컴포넌트 스캔에 의해 자동으로 빈이 등록될 때 이름이 같은 경우 `ComflictingBeanDefinitionException` 예외가 발생한다.

### 수동 빈 등록 vs 자동 빈 등록
- 수동 빈 등록이 우선이다.
  - 수동 빈이 자동 빈을 오버라이딩 한다.