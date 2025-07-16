# 평가 (Evaluation)
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [분류 성능 평가 지표](#분류classification-성능-평가-지표)
2. []()
3. []()

## 분류(Classification) 성능 평가 지표
- **Accuracy**
    - 데이터 불균형 상황에서는 잘 사용 X
- **Confusion Matrix**
    
    |  | Pred - Negative(0) | Pred - Positive(1) |
    | --- | --- | --- |
    | **Actual - Negative(0)** | TN <br> (True Negative) | FP <br> (False Positive) |
    | **Actual - Positive(1)** | FN <br> (False Negative) | TP <br> (True Positive) |
- **Precision**
    - **예측을 Positive로 한 대상** 중에 예측과 실제값이 Positive로 일치한 데이터의 비율
- **Recall**
    - **실제 값이 Positive인 대상** 중에 예측과 실제 값이 Positive로 일치한 데이터의 비율
- Precision ↔ Recall **Trade-off**
    - 이 둘은 상호 보완적인 평가 지표이기 때문에 트레이드오프 성질을 가짐
    - 분류하려는 업무의 특성상 precision이나 recall이 강조되어야 할 때가 있음 → **결정 임곗값(Threshold)** 조절
        - Threshold가 낮아질수록 Positive로 예측할 확률 증가 → **recall 증가**
    - `predict_proba()`: 0이 될 확률, 1이 될 확률 반환
    - `Binarizer(threshold=)` : 기준값보다 같거나 작으면 0, 크면 1 반환
    - `precision_recall_curve()` : 여러 threshold에 대한 precision, recall 계산
- f1-score
- ROC AUC

