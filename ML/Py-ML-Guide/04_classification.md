# 분류(Classification)
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [Decision Tree(결정 트리)](#decision-tree결정-트리)
2. [Ensemble(앙상블)](#ensemble앙상블)
3. []()

- 학습 데이터로 주어진 데이터의 feature와 label 값을 머신러닝 알고리즘으로 학습 → 모델 생성 → 미지의 값 예측

## Decision Tree(결정 트리)

- 학습을 통해 트리 기반의 분류 규칙을 만듦
- 쉽고 유연, 데이터의 스케일링이나 정규화 등 사전 가공 영향 적음
- 복잡한 규칙 구조 → 과적합 가능성
    - **약한 모델을 학습해 보완하는 앙상블** 기법에서는 장점으로 작용

### **균일도 측정**

- information gain
    - 1 - 엔트로피
    - 엔트로피(entropy): 데이터 집합의 혼잡도
- Gini index
    - 데이터가 균일할수록 낮음

### 시각화

<img width="792" height="673" alt="Image" src="https://github.com/user-attachments/assets/a5125cf1-6298-4d22-8420-5d9a778ef019" />

- `petal length (cm) <= 2.45` 와 같은 조건: 자식 노드를 만들기 위한 규칙 조건. 이 조건이 없으면 leaf node
- `gini` : value = []로 주어진 데이터 분포에서의 지니 계수
- `samples` : 현 규칙에 해당하는 데이터 건수
- `value = []` : 클래스 값 기반의 데이터 건수
- `class` : value 리스트에서 가장 많은 건수를 가진 결정 값

### 주요 하이퍼파라미터

- `max_depth` : 트리의 최대 깊이
    - default = None
- `max_features` : 최적 분할을 위해 고려할 최대 feature 개수
    - default = None
- `min_samples_split` : 노드 분할을 위한 최소한의 샘플 데이터 수 (과적합 제어)
    - default = 2
- `min_samples_leaf` : 분할 시 브랜치 노드에서 가져야 할 최소한의 샘플 데이터 수
- `max_leaf_nodes` : 말단 노드의 최대 개수

### 과적합

- 하이퍼파라미터로 트리의 깊이을 제어하지 않으면 다음과 같이 과적합 발생 가능
    - train 데이터에는 잘 맞을지라도 test 데이터에서는 잘 맞지 않음
  
<img width="450" height="331" alt="Image" src="https://github.com/user-attachments/assets/36e3ba29-a4b4-4a9c-a972-882bec66ccff" />

## 결정 트리 실습 - Human Activity Recognition

```python
feature_name_df = pd.read_csv('./human_activity/features.txt',
                              sep='\s+', 
                              header=None, 
                              names=['column_index','column_name'])
```

- `sep='\s+'` : 하나 이상의 공백을 구분자로 사용
    - `\s` : 공백
    - `+` : 하나 이상
- `header=None` : 헤더 없음 (첫 줄부터 읽음)
- `names=[]` : 컬럼 이름 지정

```python
ftr_importances_values = best_df_clf.feature_importances_
```

- 특성 중요도 값 추출

## Ensemble(앙상블)

→ 여러 개의 분류기를 생성하고 예측을 결합

- 단일 모델의 약점을 다수의 모델을 결합해 보완
- (성능이 뛰어난 모델로 구성하는 것보다) 성능이 떨어져도 서로 다른 유형의 모델을 섞는 것이 도움됨
- 거의 모두 **Decision Tree 알고리즘** 기반
    - 결정 트리의 단점인 과적합 보완
- voting & bagging → 여러 개의 분류기가 투표를 통해 최종 예측을 결정

    <img width="850" height="450" alt="Image" src="https://github.com/user-attachments/assets/99e85e6c-e786-408a-9087-e0d946938e11" />
    
    Voting과 Bagging 차이점 (출처: [https://daebaq27.tistory.com/32](https://daebaq27.tistory.com/32))

### Voting

- 일반적으로 서로 다른 알고리즘을 가진 분류기를 결합
- **Hard Voting: 다수결**
- **Soft Voting: 확률을 평균**내서 예측
    - `predict_proba()` 메소드로 class 별 확률 결정
    - 일반적으로 성능이 더 우수

```python
# 개별 모델을 소프트 보팅 기반의 앙상블 모델로 구현한 분류기
VotingClassifier(estimators=[('LR',lr_clf),('KNN',knn_clf)], voting='soft')
```

### Bagging

- 각각의 분류기가 모두 같은 유형의 알고리즘. but, 데이터 샘플링을 다르게 해서 학습
- **bootstrapping**: 중복을 허용하면서 랜덤 샘플링. → bagging(bootstrap aggregating)
- **Random Forest:** Bagging 방식으로 학습 수행 후 Voting을 통해 예측 결정
    - 무작위로 m개의 feature 선택  → $m = \sqrt p$
    - 주요 하이퍼파라미터
        - `n_estimators` : 결정 트리의 개수 (default=100)
        - `max_features` (default=auto(sqrt))
        - `n_jobs=-1` : 모든 프로세서 사용 (default=None(1))
        - `min_weight_fraction_leaf` : leaf 노드가 되기 위한 최소 가중치를 설정해서 의미 없는 노드 생성 방지
        - `max_depth` ,  `min_samples_leaf` , etc.
    - 랜덤 샘플링이기 때문에 안 뽑힐 수 있음 → **OOB(Out-Of-Bag)**

### Boosting

- 여러 개의 약한 학습기를 순차적으로 학습, 예측하면서 가중치를 부여해 보완해나감
- **Adaboost**
    - 틀린 샘플에 더 집중하도록 가중치 조절

    <img width="831" height="502" alt="Image" src="https://github.com/user-attachments/assets/f0de95c6-a51d-4b89-8e1d-1b1d36a9a8bf" />

    Adaboost 알고리즘 (출처: [https://medium.datadriveninvestor.com/understanding-adaboost-and-scikit-learns-algorithm-c8d8af5ace10](https://medium.datadriveninvestor.com/understanding-adaboost-and-scikit-learns-algorithm-c8d8af5ace10))

- **GBM(Gradient Boost Machine)**
    - 경사 하강법을 이용해 가중치 업데이트
        1. 이전 모델 예측의 residual(오차) 계산
        2. 오차로 새로운 약한 모델 학습
        3. 이전 모델에 새 모델 예측 더함
        4. 모든 약한 모델의 가중합 **→ 최종**
    - 주요 하이퍼파라미터
        - `loss` : loss function (default=deviance)
        - `learning_rate`
        - `n_estimators`
        - `subsample` : 학습에 사용하는 데이터 샘플링 비율 (default=1)
    - 너무느리다…
- **XGBoost(eXtra Gradient Boost)**
    - 예측 성능 좋음, GBM 대비 빠름, GPU 지원, 성능 향상 기능(규제 기능(정규화), 가지치기), 편의 기능(early stopping, 교차 검증, 결손값 자체 처리), etc…
    - 주요 하이퍼파라미터
        - `reg_lambada`, `reg_alpha` : 정규화
        - `colsample_bytree`, `scale_pos_weight`, `gamma`
        - `learning_rate`, `n_estimators`, `min_child_weight`, etc..
    
    ```python
    ## 파이썬 Native XGBoost 적용
    import xgboost as xgb
    from xgboost import plot_importance
    
    # 학습과 예측 데이터셋을 DMatrix로 변환
    dtr = xgb.DMatrix(data=X_tr, label=y_tr)
    dval = xgb.DMatrix(data=X_val, label=y_val)
    dtest = xgb.DMatrix(data=X_test , label=y_test)
    
    # train() 사용
    xgb_model = xgb.train(params = params, dtrain=dtr, num_boost_round=num_rounds, \
                          early_stopping_rounds=50, evals=eval_list )
    ```
    
    ```python
    ## 사이킷런 Wrapper XGBoost 적용
    from xgboost import XGBClassifier
    
    # fit() 사용
    xgb_wrapper = XGBClassifier(n_estimators=400, learning_rate=0.05, max_depth=3, \
    														early_stopping_rounds=50, eval_metric="logloss")
    evals = [(X_tr, y_tr), (X_val, y_val)]
    xgb_wrapper.fit(X_tr, y_tr, eval_set=evals, verbose=True)
    ```
    
- **Early Stopping**
    - 특정 횟수 만큼 더 이상 감소하지 않으면 조기 종료
    - 학습 시간 단축
    - 주요 파라미터
        - `early_stopping_rounds` : 최대 반복 횟수
        - `eval_metric` : 비용 평가 지표
        - `eval_set` : 평가를 수행하는 검증 데이터셋