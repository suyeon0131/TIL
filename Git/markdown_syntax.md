# markdown 문법 정리

기억용으로 적은거라 불친절 할 수 있음

## 코드
### 1. 들여쓰기
위 아래 한 줄씩 띄어쓰고 탭으로 들여쓰기 하면 코드 블럭 만들어짐
```
안녕

    안녕

안녕
```
적용하면,

안녕

    안녕

안녕

### 2. 코드 사용
` ``` ` 사용

<pre>
<code>
```
public class BootSpringBootApplication {
  public static void main(String[] args) {
    System.out.println("Hello, suyeon");
  }
}
```
</code>
</pre>

```
public class BootSpringBootApplication {
  public static void main(String[] args) {
    System.out.println("Hello, suyeon");
  }
}
```

` ``` ` 앞에 언어를 선언해 문법 강조 가능! (짱이당)

<pre>
<code>
```java
public class BootSpringBootApplication {
  public static void main(String[] args) {
    System.out.println("Hello, suyeon");
  }
}
```
</code>
</pre>

```java
public class BootSpringBootApplication {
  public static void main(String[] args) {
    System.out.println("Hello, suyeon");
  }
}
```

## 표
테이블 헤더를 구분하기 위해 3개 이상의 `-` 기호 사용

`:` 기호로 정렬 가능

- `---`, `:---` : 왼쪽 정렬
- `:---:` : 가운데 정렬
- `---:` : 오른쪽 정렬

가장 왼쪽과 가장 오른쪽에 있는 `|` 기호는 생략 가능

```
| 헤더 | 헤더 | 헤더 |
| --- | --- | --- |
| 셀 | 셀 | 셀 |
| 셀 | 셀 | 셀 |

헤더 | 헤더 | 헤더
--- | --- | ---
셀 | 셀 | 셀
셀 | 셀 | 셀
```
```
음식|정도|비고
---|:---:|---:
`치킨`|오늘 먹어서 별로 안 먹고 싶음|쿨타임 긴 편
`피자`|나우어스 피자 가고 싶당|
`떡볶이`|오늘 먹었지만 다음주 쯤 다시 먹고 싶을듯|쿨타임 짧은 편
`생맥주`|항상 먹고 싶음 지금 당장 원샷 가능|
```

음식|정도|비고
---|:---:|---:
`치킨`|오늘 먹어서 별로 안 먹고 싶음|쿨타임 긴 편
`피자`|나우어스 피자 가고 싶당|
`떡볶이`|오늘 먹었지만 다음주 쯤 다시 먹고 싶을듯|쿨타임 짧은 편
`생맥주`|항상 먹고 싶음 지금 당장 원샷 가능|

## 인용
`>` 사용
```
> hi
>> hi
>>> hi
```
> hi
>> hi
>>> hi

## 링크
```
[keyword](link)
```

## 이미지
```
![text](path)

예시
![text](/path/to.img.jpg)
```

사이즈 조절하려면 `img width="" height=""></img>` 사용해야 하는데 번거로우니까 하지말장..

++ 2025.01.20   
마크다운에 이미지 첨부할 때 이미지 파일을 깃허브에 올려서 첨부했었는데 매우매우 쉬운 방법을 발견했다...! 이렇게 배우는 거지~   
[베리이지하게 마크다운에 이미지 첨부하기](https://devdharu.tistory.com/entry/GitHub-README%EC%97%90-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%98%AC%EB%A6%AC%EB%8A%94-%EB%B0%A9%EB%B2%95)

## 줄바꿈
실제로 줄을 바꾸거나 띄어쓰기 3번 이상 하면 됨

## 목차
[vscode 목차 자동 작성](https://jacking75.github.io/VS_code_20221217/)

---

# 참고문서
[ihoneymon의 마크다운 작성법](https://gist.github.com/ihoneymon/652be052a0727ad59601)

[마크다운 사용법 총정리](https://www.heropy.dev/p/B74sNE)