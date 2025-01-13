---
layout: post
title: "Model Drift"
category: [Machine Learning] 
tags: [ML]
comments: true
published: true
---

> 📚 모델을 언제 재학습 시켜야할까? (feat. Model Drift)

# Data Drift 란?

## 1. Data Drift의 종류

- 데이터 드리프트(Data Drift): 시간이 지남에 따라 input의 분포 f(X)가 변하는 현상
- 라벨 드리프트(Label Drift): 시간이 지남에 따라 target의 분포 f(y)가 변하는 현상
- 컨셉 드리프트(Concept Drift): 시간이 지남에 따라 input과 target의 관계 f(y\|X)가 변하는 현상
    - 예시 : 거시경제 요인으로 인해 대출이 더 위험해지고 대출을 받을 수 있는 기준이 높아지는 경우 <br> → 이전에 신용도가 높은 것으로 간주되던 소득 수준이 더 이상 신용도가 높지 않게 됨

- 예측 드리프트(Prediction Drift) : 모델 예측에 변화가 생기는 것
    - 예시 : 더 부유한 지역에서 제품을 출시했을 때 신용 가치가 있는 신청의 비율이 더 높아지는 현상 <br> → 모델은 여전히 유효하지만 비즈니스가 이 시나리오에 대비하지 않았을 수 있음

![model-drift](/images/model_drift.png)

#### Data Drift 확인 쿼리 예 (bqml)

```sql
SELECT *
FROM ML.VALIDATE_DATA_SKEW(
    MODEL `project.dataset.model_w1`,
    (SELECT * FROM `project.dataset.input_dataset_w1`),
    STRUCT(
        0.05 AS categorical_default_threshold,
        0.05 AS numerical_default_threshold,
        'jensen_shannon_divergence' AS numerical_metric_type
    )
)
```
---

## 2. Data Drift 확인 방법

- 목적 : 주별 예측에 사용된 데이터가 학습데이터 기준, 얼마나 변화했는지 보기 위함
- 확인하는 지표
    - 피처별 기술통계량 : count, mean, std, min, max, median, 결측치 비율 등
    - 피처별 분포 히스토그램 (+ kde)
    - 피처별 KL-divergence 값 등

### 🧐 특징 간 거리 계산 또는 유사도 측정 방법 <br>
> L∞ 거리와 JSD는 각각 범주형 및 수치형 데이터의 특성을 반영하여 데이터 간 차이를 분석하는 데 쓰임

#### (1) L-infinity Distance (L∞ Distance) for Categorical Features
- L-infinity 거리는 벡터 간의 가장 큰 차이를 측정하는 방법이다.
- 범주형 데이터에서 특정 범주의 상대 빈도 차이가 훈련 데이터와 검증 데이터 간에 가장 큰 경우를 탐지한다.
    - 상대 빈도: 특정 범주의 빈도 / 전체 빈도

#### (2) Jensen-Shannon Divergence for Numeric Features
- JSD는 두 확률 분포 간의 차이를 측정하는 방법으로, 주로 수치형 데이터에서 사용된다.
- 데이터를 확률 분포 형태로 해석한 뒤, 분포 간 유사도 또는 차이를 계산한다.
- JSD의 값은 [0, 1] 범위를 가지며, 0에 가까울수록 두 분포가 유사하다는 것을 의미한다.
- [참고자료](https://itsudit.medium.com/the-jensen-shannon-divergence-a-measure-of-distance-between-probability-distributions-23b2b1146550) : The Jensen-Shannon Divergence: A Measure of Distance Between Probability Distributions