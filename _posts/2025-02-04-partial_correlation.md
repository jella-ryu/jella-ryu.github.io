---
layout: post
title: "Partial Correlation"
category: [Statistics] 
tags: [Statistics]
comments: true
published: true
---

> 📚 단순 상관에서 교란 변수의 영향을 어떻게 배제할 수 있을까?

## 상관관계(Correlation)
- 상관관계는 두 변수 간 관게의 강도를 측정하는 데 단순하면서도 효과적이다.
- 그러나 다른 변수들의 영향을 고려하지 못한다는 한계가 있어, "상관관계는 인과관계가 아니다"라는 통계학의 유명한 말도 있다.

## 부분 상관(Partial Correlation)
- 이 한계를 해결하는 방법이 **부분 상관**으로, 다른 변수들의 영향을 제거하고 두 변수 간의 관계를 측정하는 방법이다.
- 부분 상관은 현재 파이썬에서는 [Pingouin 라이브러리](https://pingouin-stats.org/build/html/index.html)에서만 구현되어 있다.

### 예시 데이터셋
- National Longitudinal Survey Young Men Cohort 라는 예시 데이터를 가져올 것이다.
- 이 데이터는 causaldata라는 파이썬 라이브러리에서 다운 받을 수 있다.
- 출처: 1981년 수집된 데이터 (현실 반영 X, 연습용으로 적합)
- 데이터 크기: 3,010명 (각 행은 한 명의 젊은 미국 남성을 나타냄)
- 변수(8개):

| 변수명 | 설명 |
|--------|------|
| Wage | 임금 ($ 단위) |
| Education | 교육 연수 (몇 년 동안 교육을 받았는지) |
| Experience | 근무 경험 연수 |
| Black | 흑인 여부 (1: 흑인, 0: 비흑인) |
| South | 미국 남부 거주 여부 (1: 남부, 0: 그 외) |
| Married | 결혼 여부 (1: 기혼, 0: 미혼) |
| Urban | 도심 거주 여부 (1: 도심 거주, 0: 그 외) |
| NearCollege | 해당 카운티에 4년제 대학이 있는지 여부 (1: 있음, 0: 없음) |

- 👆 위 데이터를 바탕으로 Wage(임금)을 나머지 7개 변수로 설명하는 것이 목표이다.
- 특히, 상관관계와 **부분 상관(Partial Correlation)**을 분석하여 변수 간 관계를 보다 정확히 파악해보자!

---

### 📊 단순 상관 분석 결과
- 변수별 임금(Wage)과의 단순 상관관계 그래프를 시각화하여 정렬 (절대값 기준, 큰 순서대로) 하면 다음과 같다.
- 교육 연수(Education)와 임금(Wage)이 양의 상관관계를 가지는 것은 직관적으로 이해 가능하다.
- 그런데, 근무 경험(Experience)과 임금(Wage) 간 상관관계가 거의 없는 것은 예상 밖이다!
- 대학 근접 여부(NearCollege)와 임금(Wage) 간 양의 상관관계가 나타나는 것도 직관과 다르게 느껴진다.

![상관관계 분석 결과](/images/corr.webp)

- 이러한 문제의 원인은, 단순 상관은 단일 변수 간 관게만 측정하기 때문이다.
- 다른 변수들의 영향을 고려하지 않기 때문에, 직관과 밎지 않는 결과가 나올 수 있는 것이다.
<br>

> ⚠️ **단순 선형 회귀를 해본다면?** <br>
- 선형 회귀(Linear Regression)에서 회귀 계수는 더 직관적인 방향성을 가진다.(+/- 의 영향 판단 가능)
- 단순 상관은 -1과 1 사이의 숫자로 표현되어 절대적인 관게의 강도를 비교하기 쉽다. 하지만, 회귀계수는 모두 다른 측정 단위와 스케일로 표현되므로 비교하기 적합하지 않다.
- 따라서 단순 상관이 **해석이 용이하다는 점**과, 회귀 분석에서 가능한 **각 변수의 독립적 영향 분석**, 이 두 가지 장점을 모두 갖춘 것이 바로 부분 상관이다. <br>

👉 부분 상관을 사용하면 변수 간 관계의 강도를 비교하면서도, 다른 변수들의 영향을 배제할 수 있는 것이다!

---

### 📊 부분 상관 분석 결과

#### ✅ 다시 단순 상관에서 살펴보면
- Education은 Wage와 양의 상관관계가 있고 Education과 Experience는 음의 상관관계가 있다. 
- 이는 데이터가 젊은 남성들만 포함 → 젊은 사람들은 오래 교육을 받으면 근무 경험이 적고, 반대의 경우도 마찬가지이기 때문임을 추측할 수 있다. 이 문제로 인해 Experience-Wage 상관이 왜곡되는 것이다.
- 따라서, Education이 Experience-Wage 관계를 방해하는 **"교란 변수(Confounder)" 역할**을 한다!
- 결과적으로 Experience와 Wage 간의 실제 관계가 단순 상관으로는 제대로 측정되지 않은 것이다.

#### ✅ 부분 상관을 사용하면
- Experience와 Wage의 관계를 측정할 때, Education의 영향을 제거하여 볼 수 있는 것이다. 이를 통해 보다 정확한 변수 간 관계를 파악할 수 있다.

---

### 📌 부분 상관(Partial Correlation) 알고리즘

#### 1️⃣ 단계: 두 개의 선형 회귀 모델 학습
- Experience ~ Education (근무 경험을 교육 연수로 예측)
- Wage ~ Education (임금을 교육 연수로 예측)

```python
from sklearn.linear_model import LinearRegression

lr_experience = LinearRegression().fit(X=df[["Education"]], y=df["Experience"])
lr_wage = LinearRegression().fit(X=df[["Education"]], y=df["Wage"])
```

#### 2️⃣ 단계: 잔차(residual) 계산
- 각 모델에서 예측 오차 계산
- 예측 오차: 실제 값 - 예측 값

#### 3️⃣ 단계: 예측 오차 간 상관관계 계산
- 잔차(Residual): 실제 값 - 예측 값
- 즉, Education을 고려했을 때 Experience와 Wage에서 남는 부분(순수한 차이)
```python
residual_experience = df["Experience"] - lr_experience.predict(df[["Education"]])
residual_wage = df["Wage"] - lr_wage.predict(df[["Education"]])
```

#### 4️⃣ 단계: 잔차 간 단순 상관 계산
- Education의 영향을 제거한 후 남은 Experience와 Wage의 관계를 단순 상관으로 계산
```python
partial_correlation = residual_experience.corr(residual_wage)
```

이 과정을 직관적으로 이해하면 다음과 같다.
> 예를 들어, 한 사람이 15년간 교육을 받았다고 가정하면, <br>
- Step 1에서 학습한 회귀 모델을 사용해, 이 사람의 **예상 경력(Experience)** 및 **예상 임금(Wage)**을 예측
- Step 2에서는 실제 값과 비교하여, **Education으로 설명되지 않는 차이(잔차)**를 추출
- Step 3에서 잔차 간 상관을 계산하여, Education을 제거한 후 순수한 Experience-Wage 관계를 확인

---

### 📌 가장 중요한 교란 변수(Confounders) 찾기: Forward Selection 방법
- 교란 변수(Confounders)를 찾는 방법 중 하나는 Forward Selection(순방향 선택) 절차를 활용하는 것이다
- Forward Selection 절차 <br>
    - 1️⃣ 초기 상태: 교란 변수(조정할 변수)가 없는 상태에서 시작
    - 2️⃣ 각 변수 추가: 한 번에 하나씩 변수를 추가하며 **부분 상관(Partial Correlation)**을 계산
    - 3️⃣ 가장 큰 변화 유발 변수 선택: 부분 상관이 가장 크게 변하는 변수를 교란 변수로 추가
    - 4️⃣ 반복: 모든 변수를 추가할 때까지 이 과정을 반복
    - 5️⃣ 결과 해석: 가장 큰 영향을 준 변수는 가장 중요한 교란 변수라고 볼 수 있음

<br>
이 과정을 그래프로 표현하면 다음과 같다.
<img src="/images/partial_corr.webp" alt="부분 상관 결과" width="700"/>

- 위 예시를 보면, NearCollege와 Wage의 단순 상관 → 16%
- 모든 변수를 통제한 후 부분 상관 → 2% (거의 상관 없음)
- 이러한 결과의 주된 원인 → 상관 감소의 대부분이 Urban(도심 거주 여부) 및 South(미국 남부 거주 여부) 때문이다!

---
[참고자료](http://127.0.0.1:4000/statistics/partial_correlation/)