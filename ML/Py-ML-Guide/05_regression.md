# 회귀(Regression)
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [Linear Regression](#선형-회귀)
2. []()
3. []()
4. []()
5. []()

- 데이터 값이 평균과 같은 일정한 값으로 돌아가려는 경향을 이용한 통계학 기법
- 여러 개의 독립변수와 한 개의 종속변수 간의 상관관계를 모델링하는 기법
- 핵심 → **주어진 feature와 결정 값 데이터 기반에서 학습을 통해 최적의 회귀 계수를 찾아내는 것**
- 독립변수 개수
    - 1개 → 단일 회귀
    - 여러 개 → 다중 회귀
- 회귀 계수의 결합
    - 선형 → 선형 회귀
    - 비선형 → 비선형 회귀

## 선형 회귀

- 일반 선형 회귀
    - 예측 값과 실제 값의 RSS(Residual Sum of Squares)를 최소화할 수 있도록 회귀 계수를 최적화하며, 규제를 적용하지 않은 모델
- Ridge: 선형 회귀 + L2 규제
- Lasso: 선형 회귀 + L1 규제
- Logistic Regression: 이름은 회귀이지만, 분류에 사용되는 선형 모델

> 최적의 회귀 모델을 만든다는 것   
**전체 데이터의 잔차(오류 값) 합이 최소가 되는 모델을 만드는 것**   
동시에 **오류 값 합이 최소가 될 수 있는 최적의 회귀 계수를 찾는 것**
> 

### 단순 선형 회귀

- **RSS**
    - 오류 값의 제곱을 구해서 더함
    - 미분 등의 계산 편리
    - 변수가 W0, W1인 식으로 표현 가능
    
    <img width="519" height="95" alt="Image" src="https://github.com/user-attachments/assets/036041f7-6348-45af-b260-b0b5eef8cb01" />
    
    - **회귀식의 독립변수 X, 종속변수 Y가 아니라, w 변수(회귀 계수)가 중심 변수임을 인지하는 것이 매우 중요**
    - 회귀에서 RSS는 비용(Cost)이며, w 변수로 구성되는 RSS를 **비용 함수(Cost function)**라고 함
    - 비용 함수가 반환하는 값(오류 값)을 지속해서 감소시키고 최종적으로는 더 이상 감소하지 않는 최소의 오류 값을 구함 ( == 손실 함수)
- **경사 하강법**
    - 미분 → 점진적으로 반복적인 계산을 통해 W 파라미터 값을 업데이트하면서 오류 값이 최소가 되는 W 파라미터를 구함
- RSS의 편미분
    
    <img width="627" height="130" alt="Image" src="https://github.com/user-attachments/assets/b4750628-c616-4bad-b0c6-15be860ff9ef" />
    

```python
# w1과 w0를 업데이트 할 w1_update, w0_update를 반환. 
def get_weight_updates(w1, w0, X, y, learning_rate=0.01):
    N = len(y)
    # 먼저 w1_update, w0_update를 각각 w1, w0의 shape와 동일한 크기를 가진 0 값으로 초기화
    w1_update = np.zeros_like(w1)
    w0_update = np.zeros_like(w0)
    
    # 예측 배열 계산하고 예측과 실제 값의 차이 계산
    y_pred = np.dot(X, w1.T) + w0
    diff = y-y_pred
         
    # w0_update를 dot 행렬 연산으로 구하기 위해 모두 1값을 가진 행렬 생성 
    w0_factors = np.ones((N,1))

    # w1과 w0을 업데이트할 w1_update와 w0_update 계산
    w1_update = -(2/N)*learning_rate*(np.dot(X.T, diff))
    w0_update = -(2/N)*learning_rate*(np.dot(w0_factors.T, diff))    
    
    return w1_update, w0_update
```

- 모든 데이터를 사용하면 계산량이 많아져 시간이 오래 걸림 → 일부 데이터만으로도 충분~ → **Mini-batch** !

### Linear Regression

- 사이킷런 LinearRegression 클래스
    - 예측 값과 실제 값의 RSS를 최소화하는 OLS(Ordinary Least Squares) 추정 방식으로 구현한 클래스
    - fit() 메서드로 X, y 배열을 입력 받으면 회귀 계수인 W를 `coef_` 속성에 저장
    - 입력 파라미터
        - `fit_intercept` : 절편을 적용 할지 말지 (default=True)
            - False → 0으로 지정
        - `normalize` : 입력 데이터 셋을 정규화 할지 말지 (default=False)
            - 헷갈리니까 디폴트로 냅두자
    - 속성
        - `coef_` , `intercept_`
- 선형 회귀의 다중 공선성 문제
    - 일반적으로 선형 회귀는 입력 feature의 **독립성**에 많은 영향을 받음.
    - feature 간의 상관 관계가 매우 높은 경우 분산이 매우 커져서 오류에 매우 민감해짐 → **다중 공선성 문제**
        - 상관 관계가 높은 feature가 많은 경우 독립적인 **중요한 feature만 남기고 제거하거나 규제를 적용**

### 회귀 평가 지표

| 평가 지표 | 설명 |
| --- | --- |
| **MAE(Mean Absolute Error)** | 실제 값과 예측 값의 차이를 절댓값으로 변환해 평균 |
| MSE(Mean Squared Error) | 실제 값과 예측 값의 차이를 제곱해 평균 |
| MSLE | MSE에 로그 적용. 일부 큰 오류 값들이 전체 오류 값을 증가시키는 것을 방지 |
| **RMSE** | MSE에 루트 적용 |
| **RMSLE** | RMSE에 로그 적용 |
| R^2 | 실제 값의 분산 대비 예측 값의 분산 비율. 1에 가까울수록 예측 정확도 높음. |
- MAE vs. RMSE
    - MAE에 비해 **RMSE**는 상대적으로 더 큰 오류 값이 있을 때 이에 대한 페널티를 더 부과함
        - 즉, 다른 값에 비해 큰 오류 값이 존재하는 경우 RMSE 값이 더 높음
- 사이킷런 Scoring 함수에 회귀 평가 적용 시 유의 사항
    - MAE의 사이킷런 scoring 파라미터 값 → `neg_mean_absolute_error`  ⇒ **MAE * -1**이라는 뜻
        - MAE는 절댓값의 합이기 때문에 음수가 될 수 없음
        
        → scoring 함수가 score 값이 클수록 좋은 평가 결과로 인식하기 때문 → -1을 곱해서 작은 오류 값이 더 큰 숫자로 인식되게 함