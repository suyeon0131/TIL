# 사이킷런으로 시작하는 머신러닝
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [붓꽃 품종 예측하기](#붓꽃-품종-예측하기)
2. [Model Selection](#model-selection)
3. []()

### 사이킷런
- 머신러닝을 위한 다양한 알고리즘과 개발을 위한 편리한 프레임워크와 API 제공
  - 가장 파이썬스러운 API

## 붓꽃 품종 예측하기

```python
X_train, X_test, y_train, y_test = train_test_split(iris_data, iris_label,
                                                  test_size=0.2, random_state=11)
```

- X_train: 학습용 feature, X_test: 테스트용 feature
- y_train: 학습용 target, y_test: 테스트용 target

### 학습(train)

```python
# DecisionTreeClassifier 객체 생성 
dt_clf = DecisionTreeClassifier(random_state=11)

# 학습 수행 
dt_clf.fit(X_train, y_train)
```

- `random_state=11` : 결과를 일정하게 유지 (무슨 숫자이든 상관없음)

### 예측(predict)

```python
# 학습이 완료된 DecisionTreeClassifier 객체에서 테스트 데이터 세트로 예측 수행
pred = dt_clf.predict(X_test)
```

- 예측 정확도 평가
    
    ```python
    print('예측 정확도: {0:.4f}'.format(accuracy_score(y_test,pred)))
    ```
    

### Estimator - fit(), predict()

- **Classifier**
    - DecisionTreeClassifier
    - RandomForestClassifier
    - GradientBoostingClassifier
    - GaussianNB
    - SVC
- **Regressor**
    - LinearRegression
    - Ridge
    - Lasso
    - RandomForestRegressor
    - GradientBoostingRegressor

### 데이터 구성

- data: feature의 데이터셋
- target: 분류일 때 레이블 값, 회귀일 때는 숫자 결과 값 데이터셋
- target_names: 개별 레이블의 이름
- feature_names: feature의 이름
- DESCR: 데이터셋에 대한 설명과 각 feature의 설명

## Model Selection

### K-fold cross validation(교차 검증)

- 일반 K-fold
    
    ```python
    from sklearn.model_selection import KFold
    
    # 5개의 폴드 세트로 분리하는 KFold 객체와 폴드 세트별 정확도를 담을 리스트 객체 생성.
    kfold = KFold(n_splits=5)
    
    for train_index, test_index in kfold.split(features): 
    ...
    ```
    
- Stratified K-fold
    - 학습 데이터와 검증 데이터가 가지는 레이블 분포도가 유사하도록 검증 데이터 추출 → imbalanced 데이터셋에 적합
    
    ```python
    from sklearn.model_selection import StratifiedKFold
    
    skf = StratifiedKFold(n_splits=3)
    
    for train_index, test_index in skf.split(iris_df, iris_df['label']):
    ...
    ```
    
- **`cross_val_score()`** : fold 셋 추출, 학습/예측, 평가를 한 번에 수행
    
    ```python
    # 성능 지표는 정확도(accuracy) , 교차 검증 세트는 3개 
    scores = cross_val_score(dt_clf, data, label, scoring='accuracy', cv=3)
    ```
    

### GridSearchCV

- 교차 검증 + 최적 하이퍼파라미터 튜닝
- 파라미터 순차 적용 횟수 X CV 세트 수 = 학습/검증 총 수행 횟수

```python
# parameter들을 dictionary 형태로 설정
parameters = {'max_depth':[1, 2, 3], 'min_samples_split':[2,3]}

# (3 * 2) * 3 = 18
grid_dtree = GridSearchCV(dtree, param_grid=parameters, cv=3,
                                 refit=True, return_train_score=True)
```

- 파라미터에서 value 값은 **무조건 list**
- `refit=True` : 가장 좋은 파라미터 설정으로 재학습 (defalut)
    - fit() 수행 시, 학습이 완료된 estimator를 포함하고 있으므로 predict()를 통해 예측 가능