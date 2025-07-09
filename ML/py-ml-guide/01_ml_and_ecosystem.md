# 파이썬 기반의 머신러닝과 생태계 이해
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [머신러닝의 개념](#머신러닝의-개념)
2. [넘파이 ndarray](#넘파이-ndarray)
3. []()
4. []()
5. []()
6. []()
7. []()

## 머신러닝의 개념
- 애플리케이션을 수정하지 않고도 데이터를 기반으로 패턴을 학습하고 결과를 추론하는 알고리즘 기법

### 유형 (by 마스터 알고리즘)
- 기호주의: 결정 트리, etc..
- 연결주의: 신경망/딥러닝
- 유전 알고리즘
- 베이지안 통계
- 유추주의: KNN, 서포트 벡터 머신

### 단점
- 데이터에 너무 의존적 (garbage in, garbage out)
- 과적합 쉬움
- 복잡한 알고리즘으로 인해 도출된 결과에 대한 논리적인 이해가 어려움 (머신러닝 == 블랙박스)
- 데이터만 집어 넣으면 자동으로 최적화된 결과를 도출할 것 -> **환상!**

## 넘파이 ndarray
- **ndarray**: N 차원 배열 객체

array | 차원 | shape
:---: | :---: | :---:
`[1 2 3]` | 1차원 | **(3,)**
`[[1 2 3]` <br> `[4 5 6]]` | 2차원 | (2, 3)

- ndarray 내의 데이터 값은 숫자, 문자, bool 모두 가능
- but, 데이터 타입은 **같은 데이터 타입만** 가능
  - 한 개의 ndarray 객체에 int와 float가 함께 있을 수 없음

### 타입(type) 변환
- `astype()`: 변경을 원하는 타입을 인자로 입력

```python
list2 = [1, 2, 'test']
array2 = np.array(list2)
print(array2, array2.dtype)

list3 = [1, 2, 3.0]
array3 = np.array(list3)
print(array3, array3.dtype)
```
```
['1' '2' 'test'] <U21
[1. 2. 3.] float64
```   
-> 자동 형변환

```python
array_int = np.array([1, 2, 3])
array_float = array_int.astype('float64') # array_int.astype(np.float64)
print(array_float, array_float.dtype)

array_int1= array_float.astype('int32')
print(array_int1, array_int1.dtype)

array_float1 = np.array([1.1, 2.1, 3.1])
array_int2= array_float1.astype('int32')
print(array_int2, array_int2.dtype)
```
```
[1. 2. 3.] float64
[1 2 3] int32
[1 2 3] int32
```

### axis 축
- ndarray의 shape은 행, 열, 높이 단위로 부여되는 것이 아닌, axis0, axis1과 같이 **axis 단위**로 부여

<img width="613" height="232" alt="Image" src="https://github.com/user-attachments/assets/85fe1cf5-5172-4e51-a7de-134ad09330ac" />