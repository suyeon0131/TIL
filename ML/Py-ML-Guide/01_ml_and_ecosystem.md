# 파이썬 기반의 머신러닝과 생태계 이해
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [머신러닝의 개념](#머신러닝의-개념)
2. [넘파이 ndarray](#넘파이-ndarray)
3. []()
4. []()
5. []()

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

### 타입(type) 변환 - astype()
- 변경을 원하는 타입을 인자로 입력

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

<img width="822" height="282" alt="Image" src="https://github.com/user-attachments/assets/4dfe959a-dac6-450f-a0d9-764ccde3c87f" />

### 편리하게 생성 - arange, zeros, ones
- `np.arange(10)` -> [0 1 2 3 4 5 6 7 8 9]
- `np.zeros((3,2), dtype='int32')` -> [[0 0] [0 0] [0 0]]
- `np.ones((3, 2))` -> [[1. 1.] [1. 1.] [1. 1.]]

### 차원, 크기 변경 - reshape()
- `reshape(2, 5)`: 2차원의 2x5 ndarray로 변환
- **`reshape(-1, 5)`** 와 같이 인자에 **-1**을 부여하면 -1에 해당하는 axis의 크기는 **가변적**, 해당하지 않는 axis의 크기는 **인자값**으로 고정
  
<img width="883" height="223" alt="Image" src="https://github.com/user-attachments/assets/15f74a40-5f91-453a-9b80-48ba91f69126" />

```python
array1 = np.arange(10)
print(array1)

array2 = array1.reshape(-1,5)
print('array2 shape:',array2.shape)

array3 = array1.reshape(5,-1)
print('array3 shape:',array3.shape)
```
```
[0 1 2 3 4 5 6 7 8 9]
array2 shape: (2, 5)
array3 shape: (5, 2)
```

### 인덱싱(Indexing)
- 특정 위치의 단일 값 추출
- 슬라이싱(Slicing): 연속된 인덱스 상의 ndarray 추출

    <img width="688" height="455" alt="Image" src="https://github.com/user-attachments/assets/dfa09537-cb08-45ce-afac-26ef600725da" />
    
- 팬시 인덱싱(Fancy Indexing): 일정한 인덱싱 집합을 list or ndarray 형태로 지정해 반환
    
    <img width="687" height="225" alt="Image" src="https://github.com/user-attachments/assets/cc1511a7-b8c7-43bc-b354-9cd6675a27cd" />
    
    ```python
    array1d = np.arange(start=1, stop=10)
    array2d = array1d.reshape(3,3)
    print(array2d)
    
    array3 = array2d[[0,1], 2]
    print('array2d[[0,1], 2] => ',array3.tolist())
    
    array4 = array2d[[0,1], 0:2]
    print('array2d[[0,1], 0:2] => ',array4.tolist())
    
    array5 = array2d[[0,1]]
    print('array2d[[0,1]] => ',array5.tolist())
    ```
    
    ```
    [[1 2 3]
     [4 5 6]
     [7 8 9]]
    array2d[[0,1], 2] =>  [3, 6]
    array2d[[0,1], 0:2] =>  [[1, 2], [4, 5]]
    array2d[[0,1]] =>  [[1, 2, 3], [4, 5, 6]]
    ```
    
- **불리언 인덱싱(Boolean Indexing)**: 특정 조건에 해당하는지 true/false 값 인덱싱 집합을 기반으로 true 위치의 ndarray 반환
    
    ```python
    array1d = np.arange(start=1, stop=10)
    print(array1d)
    # [ ] 안에 array1d > 5 Boolean indexing을 적용 
    array3 = array1d[array1d > 5]
    print('array1d > 5 불린 인덱싱 결과 값 :', array3)
    ```
    
    ```
    [1 2 3 4 5 6 7 8 9]
    array1d > 5 불린 인덱싱 결과 값 : [6 7 8 9]
    ```
    

### 배열의 정렬 - sort(), argsort()

- `np.sort(ndarray)` : 인자로 들어온 원 행렬은 그대로 유지 → 원 행렬의 정렬된 행렬 반환
- `ndarray.sort()` : 원 행렬 자체를 정렬된 형태로 변환 (반환 값은 None)
    
    ```python
    sort_array1_desc = np.sort(org_array)[::-1]
    print ('내림차순으로 정렬:', sort_array1_desc) 
    ```
    
    ```
    내림차순으로 정렬: [9 5 3 1]
    ```
    
    <img width="674" height="278" alt="Image" src="https://github.com/user-attachments/assets/65f4b9d4-1633-47bb-9b2d-cfc1d19e1df7" />
    
- `np.argsort()` : 정렬 행렬의 원본 행렬 인덱스를 ndarray 형으로 반환
    
    <img width="687" height="113" alt="Image" src="https://github.com/user-attachments/assets/5221ea92-24ef-421f-b4b9-7be2ffa89d32" />
    

### 행렬 내적 - np.dot(A, B)

### 전치 행렬 - np.transpose(A)

<img width="386" height="241" alt="Image" src="https://github.com/user-attachments/assets/a4e98daf-68fb-47ef-84fd-e97533030e46" />