# 분류(Classification)
> 본 게시물은 권철민님의 [[개정판] 파이썬 머신러닝 완벽 가이드](https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%99%84%EB%B2%BD%EA%B0%80%EC%9D%B4%EB%93%9C/dashboard) 강의를 듣고 정리한 내용입니다.  
게시물에 포함된 코드와 이미지 등의 모든 저작권은 인프런과 권철민 강사님께 있습니다.

**Object**
1. [Decision Tree(결정 트리)](#decision-tree결정-트리)
2. []()
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

<img width="457" height="331" alt="Image" src="https://github.com/user-attachments/assets/36e3ba29-a4b4-4a9c-a972-882bec66ccff" />