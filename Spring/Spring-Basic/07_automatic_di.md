# 의존관계 자동 주입
> 본 게시물은 김영한님의 [스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 김영한 강사님께 있습니다.

**Object**
1. [다양한 의존관계 주입 방법](#다양한-의존관계-주입-방법)
2. [옵션 처리](#옵션-처리)
3. [생성자 주입을 선택하라!](#생성자-주입을-선택하라)
4. [롬복과 최신 트렌드](#롬복과-최신-트렌드)
5. [@Autowired 필드 명, @Qualifier, @Primary](#autowired-필드-명-qualifier-primary)
6. [애노테이션 직접 만들기](#애노테이션-직접-만들기)
7. [조회한 빈이 모두 필요할 때, List, Map](#조회한-빈이-모두-필요할-때-list-map)
8. [자동, 수동의 올바른 실무 운영 기준](#자동-수동의-올바른-실무-운영-기준)

## 다양한 의존관계 주입 방법
1. 생성자 주입
2. 수정자 주입(setter 주입)
3. 필드 주입
4. 일반 메서드 주입

### 생성자 주입
- 생성자 호출 시점에 딱 1번만 호출된다.
- **불변**, 필수 의존관계에 사용한다.

### 수정자 주입 (setter 주입)
- 선택, **변경** 가능성이 있는 의존관계에 사용한다.
- 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법이다.

### 필드 주입
- 코드가 간결하지만 외부에서 변경이 불가능해서 테스트 하기 힘들다.
- DI 프레임워크가 없으면 아무것도 할 수 없다.
  - 사용하지 말자~
```java
@Autowired private MemberRepository memberRepository;
@Autowired private DiscountPolicy discountPolicy;
```

### 일반 메서드 주입
- 한 번에 여러 필드를 주입 받을 수 있다.
- 일반적으로 잘 사용하지 않는다.

## 옵션 처리
- `@Autowired(required=false)`: 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 되지 않는다.
- `org.springframeword.lang.@Nullable`: 자동 주입할 대상이 없으면 null이 입력된다.
- `Optional<>`: 자동 주입할 대상이 없으면 `Optional.empty`가 입력된다.
- **`AutowiredTest`** 참고

```java
// 출력결과
setNoBean2 = null
setNoBean3 = Optional.empty
```

## 생성자 주입을 선택하라!

**불변**   
- 대부분의 의존관계 주입은 한 번 일어나면 애플리케이션 종료 시점까지 의존관계를 변경할 일이 없다.
- 수정자 주입은 setXxx 메서드를 public으로 열어둬야 한다. -> 좋은 설계 방법이 아니다.
- 생성자 주입은 객체를 생성할 때 1번만 호출되고 이후에 호출되는 일이 없다.

**final 키워드**
- 생성자에서 값이 설정되지 않으면 컴파일 오류로 알려준다.

## 롬복과 최신 트렌드
- 롬복 라이브러리가 제공하는 **`@RequiredArgsConstructor`** 기능을 사용하면 final이 붙은 필드를 모아서 생성자를 자동으로 만들어준다.
```java
// 이전 코드
@Component
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
```
```java
// 롬복을 적용한 이후 코드
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
}
```
- 컴파일 시점에 생성자 코드를 자동으로 생성해준다.

## @Autowired 필드 명, @Qualifier, @Primary
- 조회 대상 빈이 2개 이상일 때 해결 방법
  1. @Autowired 필드 명 매칭
  2. @Qualifier -> @Qualifier끼리 매칭 -> 빈 이름 매칭
  3. @Primary 사용

### @Autowired 필드 명 매칭
1. 타입 매칭
2. 타입 매칭의 결과가 2개 이상일 때 필드 명, 파라미터 명으로 빈 이름 매칭

### @Qualifier -> @Qualifier끼리 매칭 -> 빈 이름 매칭
- 추가 구분자를 붙여주는 방법이다.
  - 빈 이름을 변경하는 것은 아니다.
```java
@Qualifier("mainDiscountPolicy")
```
1. @Qualifier끼리 매칭
2. 빈 이름 매칭
3. `NoSuchBeanDefinitionException` 예외 발생

### @Primary 사용   
`ctrl + alt + b`: 구현체로 이동   
- 여러 빈이 매칭되면 우선 순위를 가진다.

**@Qualifier vs @Primary**   
- `@Primary`는 기본값 처럼 동작하는 것이고, `@Qualifier`는 매우 상세하게 동작한다.
- 스프링은 자동보다 수동이, 넓은 범위 보다는 좁은 범위가 우선권을 가진다. -> @Qualifier의 우선순위가 더 높다.

## 애노테이션 직접 만들기
- `@Qualifier("mainDiscountPolicy)`와 같이 문자를 적으면 컴파일 시 타입 체크가 안 된다.
  - 애노테이션을 만들어서 해결할 수 있다.
  - `mainDiscountPolicy` 참고

## 조회한 빈이 모두 필요할 때, List, Map
```java
public class AllBeanTest {

    @Test
    void findAllBean() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

        DiscountService discountService = ac.getBean(DiscountService.class);
        Member member = new Member(1L, "userA", Grade.VIP);
        int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");

        assertThat(discountService).isInstanceOf(DiscountService.class);
        assertThat(discountPrice).isEqualTo(1000);

        int rateDiscountPrice = discountService.discount(member, 20000, "rateDiscountPolicy");
        assertThat(rateDiscountPrice).isEqualTo(2000);

    }

    static class DiscountService {
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policies;

        @Autowired
        public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
            this.policyMap = policyMap;
            this.policies = policies;
            System.out.println("policyMap = " + policyMap);
            System.out.println("policies = " + policies);
        }

        public int discount(Member member, int price, String discountCode) {
            DiscountPolicy discountPolicy = policyMap.get(discountCode);
            
            return discountPolicy.discount(member, price);
        }
    }
}
```   
**로직 분석**
- DiscountService는 Map으로 모든 DiscountPolicy를 주입받는다.
- `discount()` 메서드는 discountCode로 "fixDiscountPolicy"가 넘어오면 map에서 `fixDiscountPolicy` 스프링 빈을 찾아서 실행한다.
  
**주입 분석**
- `Map<Stirng, DiscountPolicy>`: map의 키에 스프링 빈의 이름을 넣어주고, 그 값으로 DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.
- `List<DiscountPolicy>`: DiscountPolicy 타입으로 조회한 모든 스프링 빈을 담아준다.

## 자동, 수동의 올바른 실무 운영 기준
- **편리한 자동 기능을 기본으로 사용하자**
- **수동 빈 등록은 언제?**
  - 업무 로직 빈: 웹을 지원하는 컨트롤러, 핵심 비즈니스 로직이 있는 서비스, 데이터 계층의 로직을 처리하는 리포지토리 등
  - 기술 지원 빈: 기술적인 문제나 공통 관심사(AOP) 처리 -> 데이터베이스 연결, 공통 로그 처리 등
  - 애플리케이션에 광범위하게 영향을 미치는 **기술 지원** 객체는 수동 빈으로 등록해서 설정 정보에 바로 나타나게 하는 것이 유지보수 하기 좋다.
- **비즈니스 로직 중에서 다형성을 적극 활용할 때**
  - 딱 보고 이해할 수 있게 수동 빈으로 등록하거나 특정 패키지에 묶어두는 게 좋다.
- **정리**
  - 편리한 자동 기능을 기본으로 사용~!
  - 직접 등록하는 기술 지원 객체는 수동으로 등록~
  - 다형성을 적극 활용하는 비즈니스 로직도 수동 등록을 고려하자~