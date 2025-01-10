---
layout: post
title: "Model Calibration"
category: "Machine Learning" 
tags: [ML, Model Retraining]
comments: true
published: true
---

## Model Calibration이란?

- Model Calibration('모델 보정'이라고 해석됨)은 예측된 확률이나 결과의 정확도를 높이기 위해 모델의 예측을 조정하는 프로세스입니다.
- 예를 들어, 모델이 1 클래스일 확률이 0.8 이라고 예측했다면, 실제로 80% 경우에서 1 클래스여야 잘 보정된 것이라고 할 수 있습니다.
- 보정을 통해 머신러닝 모델의 예측이 신뢰할 수 있고 해석 가능한지 확인할 수 있습니다.
- Miscalibrated Model을 식별하기 위해 보통 Calibration Curve를 그리거나, Brier Score 같은 정량적 지표를 확인합니다.

---

## miscalibrated model을 식별하는 법

### 1. Calibration Curve
- 이 그래프는 모델에서 예측한 확률(Predicted Probability)을 실제 관측된 이벤트의 빈도(Observed Frequency)와 비교하여 표시합니다. 예측 확률이 실측 데이터의 빈도와 일치하는 대각선에 가까울수록 잘 보정된 것입니다.
- 이를 통해 모델의 “정확도 측면에서의 신뢰도”를 평가할 수 있습니다.

##### 👉 그래프 생성 과정
1. 예측 확률을 bins로 나눔 (보통 10개 정도의 구간으로 나눔)
2. 각 bin에 대해:
   - x축: 해당 bin의 평균 예측 확률
   - y축: 해당 bin에서 실제 양성(positive) 클래스의 비율 계산
3. 이 점들을 연결하여 곡선 생성

```python
from sklearn.calibration import calibration_curve

def plot_calibration_curve(df, true_col, pred_col, model_name):

    y_true = df[true_col]
    y_pred = df[pred_col]
    
    prob_true, prob_pred = calibration_curve(y_true, y_pred, n_bins=30, strategy='quantile') # 데이터에 따라 bin 크기 자동조절
    plt.plot(prob_pred, prob_true, marker='o', label=model_name)
    plt.plot([0, 1], [0, 1], linestyle='--', color='gray', label='Perfectly Calibrated')  # Ideal calibration line

# 각 모델의 Calibration Curve를 그리기
plt.figure(figsize=(8, 6))
plot_calibration_curve(df, true_col='label', pred_col='prob_ver1', model_name="Model 1")
plot_calibration_curve(df, true_col='label', pred_col='prob_ver2', model_name="Model 2")
plt.xlabel("Predicted Probability")
plt.ylabel("Observed Frequency")
plt.legend()
plt.title("Calibration Curves")
plt.show()
```

##### 👉 그래프 해석 
- 점들이 대각선 위에 있으면 모델이 Underconfident → 실제 가능성보다 낮은 확률을 예측하는 것이고,
- 반대로 점들이 대각선 아래에 있으면 모델이 Overconfident → 높은 확률을 예측하는 것입니다.
- 아래 예시에서 주황색 모델이 잘 보정된 경우입니다.j

![Curve Example](/images/calibration.png)

---

### 2. Brier Score
- Calibration metric 으로 Brier score 같은 정량적 지표를 함께 계산하여 그래프의 결과를 수치로 확인할 수 있습니다. 
- Brier score는 분류 문제에서 사용되며, 예측 확률값과 실제 결과 간의 평균 제곱 오차를 측정합니다. 따라서 값이 작을수록 보정이 잘 된 모델입니다. 👉 즉, 확률적 정확도를 평가합니다.
- 0에 가까운 값은 완벽한 예측을 나타내며, 1에 가까운 값은 예측의 정확도가 낮다는 것을 뜻합니다.

```python
from sklearn.metrics import brier_score_loss

# Brier Score 계산
brier_model1 = brier_score_loss(df['label'], df['prob_ver1'])
brier_model2 = brier_score_loss(df['label'], df['prob_ver2'])

print(f"Brier Score (Model 1): {brier_model1:.4f}")
print(f"Brier Score (Model 2): {brier_model2:.4f}")
```