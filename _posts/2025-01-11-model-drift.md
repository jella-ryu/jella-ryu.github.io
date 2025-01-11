---
layout: post
title: "Model Drift"
category: [Machine Learning] 
tags: [ML]
comments: true
published: true
---

> 📚 모델을 언제 재학습 시켜야할까? (feat. Model Drift)

## Data Drift 란?

### 1. Data Drift의 종류

- 데이터 드리프트(Data Drift): 시간이 지남에 따라 input의 분포 f(X)가 변하는 현상
- 라벨 드리프트(Label Drift): 시간이 지남에 따라 target의 분포 f(y)가 변하는 현상
- 컨셉 드리프트(Concept Drift): 시간이 지남에 따라 input과 target의 관계 f(y\|X)가 변하는 현상
    - 예시 : 거시경제 요인으로 인해 대출이 더 위험해지고 대출을 받을 수 있는 기준이 높아지는 경우 <br> → 이전에 신용도가 높은 것으로 간주되던 소득 수준이 더 이상 신용도가 높지 않게 됨

- 예측 드리프트(Prediction Drift) : 모델 예측에 변화가 생기는 것
    - 예시 : 더 부유한 지역에서 제품을 출시했을 때 신용 가치가 있는 신청의 비율이 더 높아지는 현상 <br> → 모델은 여전히 유효하지만 비즈니스가 이 시나리오에 대비하지 않았을 수 있음

(이미지 삽입)

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