---
layout: post
title: "Uplift Model"
category: [Machine Learning]
tags: [ML, Decision Tree]
comments: true
published: true
---

> 📚 마케팅을 수행했을 때 제일 효과 좋을 그룹을 찾고 싶다면? (feat.Decision Tree)

## Uplift Model 이란?
> 참고할 만한 라이브러리 : [CausalML](https://causalml.readthedocs.io/en/latest/causalml.html)

- 통신 회사가 customer churn(고객 이탈)을 줄이는 것이 목표인 시나리오를 생각해보자.
- 보통의 ML 기반 모델을 사용하는 경우라면, 과거의 데이터를 이용해서 현재 고객이 이탈 가능성이 있는지 예측할 것이다. 그리고 예측 결과에 기반해 이탈 위험 고객을 식별하고 맞춤형 인센티브나 프로모션을 제공하는 등 조치를 취할 것이다.

#### 그런데.. 
- 만약 그럼에도 불구하고 고객이 떠나가면? (이사 등으로 인해)
- 우편이나 전화 등으로 발송된 프로모션이 오히려 이탈 욕구를 자극한다면?
- 오히려 프로모션 같은게 없어도 남아있을 고객이 이탈해버릴 수도 있다!

---

### 그럼 어떻게 해야하지?
- 누구에게 처치(Treatement, 개입)를 할 것인가!에 대해 다음과 같은 그룹을 나누어 생각해보면 좋다.

![Classification related to uplift modeling](/images/uplift.webp)

#### ▶︎ Persuadables 🎉 → 우리가 타겟해야할 대상
- Treatment의 긍정적인 영향을 받는 사람
- 즉, 개입이 없으면 원하는 대로 반응할 가능성이 낮은 사람으로 개입의 혜택을 가장 많이 받는 그룹이다.

#### ▶︎ Sure Things
- 치료 여부와 관계없이 개입에 긍정적으로 반응하는 사람

#### ▶︎ Lost Causes
- 치료를 받더라도 개입에 긍정적으로 반응하지 않을 가능성이 높은 사람 
- 이 그룹을 식별하면 개입으로 원하는 결과를 얻을 가능성이 낮으므로 불필요한 자원 할당을 피하는 데 도움이 된다.

#### ▶︎ Sleeping Dogs / Do-Not-Disturb
- 개입에 부정적으로 반응하여 오히려 개입이 행동에 해로운 영향을 미칠 수 있는 사람
- 이러한 그룹을 인식하는 것은 의도치 않게 부정적인 결과를 초래하는 것을 방지하는 데 매우 중요하다.

---
### Uplift Model 학습을 위해 필요한 데이터

![Uplift Model Data](/images/uplift_data.webp)

- Feature columns : 모델의 입력으로 사용되는 데이터 집합에서 개인 또는 관찰의 속성 또는 특성
- Treatment indicator : 특정 개인이 개입(처치)를 받았는지 여부를 나타내는 이진 열(0, 1)
- Target column : 모델이 예측하도록 설계된 결과 변수

---

### 결과 해석 예시
- 처치(treatment)를 받은 사람과 받지 않은 사람의 차이를 비교해서 처치 효과를 측정 → 평균 치료 효과(Average Treatment Effect, ATE)를 계산
- 예를 들어, Control 그룹의 처치 효과가 0.1이고, Treatment 그룹의 처치 효과가 0.2라면, 처치 효과는 0.1이다.

---

### Tree-Based Models for Uplift Modeling

- 일반적으로 decision tree는 예측된 클래스의 '정확도'를 극대화하도록 설계되었다. 즉, Gini 지수나 Entropy 등을 이용해 분할 기준을 선택한다.
- uplift decision tree는 개입(처치)으로 인한 클래스의 확률 변화를 모델링하기 위한 것이다. 따라서 분할 기준은 왼쪽과 오른쪽의 하위 트리에서 실험군(T) 확률과 대조군(C) 확률(분포) 차이를 최대화하도록 설계되었다. 즉, 각 분할이 얼마나 효과적으로 실험군과 대조군의 반응을 구분할 수 있는지를 측정한다.
    - 예를 들어, 특정 연령대 또는 지역에서 처치 효과가 대조군에 비해 크게 나타나는 경우, 연령이나 지역을 기준으로 데이터를 나누는 것!

![Uplift Decision Tree](/images/uplift_splitting.webp)

---

#### 1. Uplift 트리 구축
- 노드 분할 기준 : 각 노드에서 **실험군(Treatment)**과 **대조군(Control)**의 분포 차이를 측정한다.
- 분포 차이를 최대화하는 특징(feature)과 분할 기준(split point)을 선택해 처리군과 대조군의 분포를 가장 효과적으로 분리하게 된다.
- 재귀적 분할 : 트리는 반복적으로 분할된다. 각 노드에서 최적의 특징과 분할 기준을 선택한 후, 설정된 조건에 따라 더 이상 분할하지 않을 때까지 반복한다.

#### 2. 분할 중단 조건
- 트리의 과적합(overfitting)을 방지하기 위해 다음 중 하나의 조건이 충족되면 분할을 멈춥니다.
    - 최대 깊이(Max Depth) 제한: 트리의 최대 깊이를 설정하여 복잡도를 조절한다.
    - 최소 샘플 개수(Min Samples per Leaf) 제한: 리프 노드에 필요한 최소 샘플 개수를 설정한다.

- 최소 향상도 조건(Minimum improvement in divergence)이 일정 임계값 이하로 떨어지면 분할을 멈춘다.

#### 3. 가지치기(Pruning)
- 트리 생성 이후, 모델 성능에 큰 영향을 미치지 않는 가지(branch)를 제거한다. 
- 이를 통해 과적합을 방지하고 새로운 데이터에 대한 일반화 성능을 향상시킨다.

#### 4. Uplift 예측
- 리프 노드 할당 : 각 관측값에 대해 트리를 따라가면서 해당 데이터가 포함될 리프 노드를 찾는다.

- Uplift 점수 계산 : 리프 노드에서 실험군과 대조군의 예측 결과를 각각 계산한다.
    1. 각 리프 노드에서 실험군과 대조군의 **평균 반응률(결과)**을 계산한다.
        - 실험군 평균 반응률 : 리프 노드 내에서 실험군 샘플의 평균 결과값
        - 대조군 평균 반응률 : 리프 노드 내에서 대조군 샘플의 평균 결과값
    2. 두 결과의 차이(실험 효과)를 Uplift 점수로 할당하여 개별 관측값에 대한 처리의 영향을 평가한다.
        - 실험군과 대조군의 평균 반응률 차이를 uplift 점수로 정의한다.

---

### 활용 : Uplift 점수 해석
> Uplift 점수가 양수(+)인 리프노드에 해당하는 고객에게 마케팅을 집행하는 것이 (일반적으로) 좋다.   
단, 샘플 크기나 비용-효과(ROI), 시장 확대 기회 등을 고려하여 최적의 마케팅 대상을 찾아야 한다.

- Uplift 점수가 양수(+): 개입이 대조군에 비해 긍정적인 영향을 미침. 
    - 👉 만약 차이가 10%라면 이 리프 노드에 속한 고객에게 광고를 집행하면, 광고를 보낸 고객이 광고를 보지 않은 고객보다 구매율이 20% 높은 것!
- Uplift 점수가 음수(-): 개입이 대조군에 비해 부정적인 영향을 미침.
- Uplift 점수가 0에 가까움: 실험군과 대조군의 차이가 거의 없음(영향이 미미함).

---

[Reference](https://towardsdatascience.com/beyond-predictions-uplift-modeling-the-science-of-influence-part-i-cb28c47d1b04)
