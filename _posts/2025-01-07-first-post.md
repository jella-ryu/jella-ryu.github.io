---
layout: post
title: "Model Calibration"
tags: [Machine Learning, Model Retraining]
comments: true
---

## Model Calibration이란?
- 보정을 통해 머신러닝 모델의 예측이 신뢰할 수 있고 해석 가능한지 확인할 수 있습니다.
- 분류와 회귀 모델 모두에서 중요합니다.

## miscalibrated model을 식별하는 법

- **reliability graph**, also known as the **calibration curve**.을 그려보기
    - 이 그래프는 모델에서 예측한 확률을 실제 관측된 이벤트의 빈도와 비교하여 표시합니다.
    - 예측 확률이 실측 데이터의 빈도와 일치하는 대각선에 가까울수록 잘 보정된 것
        - 점들이 대각선 위에 있으면 모델이 Underconfident → 실제 가능성보다 낮은 확률을 예측하는 것이고,
        - 반대로 점들이 대각선 아래에 있으면 모델이 Overconfident → 높은 확률을 예측하는 것입니다.
        - 다음은 보정되지 않은 모델의 예입니다:


![Miscalibrated Model Example](/images/calibration.png)

- 또한 Brier 점수 또는 예상 교정 오류(ECE)와 같은 통계 지표를 통해 오보정 정도를 정량화할 수 있습니다. Brier 점수 또는 ECE가 높을수록 오보정 수준이 높다는 것을 의미하며, 재보정이 필요하다는 것을 나타냅니다.