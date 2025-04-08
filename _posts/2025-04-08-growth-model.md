---
layout: post
title: "Duolingo의 사용자 세분화 모델과 CURR 전략"
category: Growth
tags: [Growth]
comments: true
published: true
---

> 📚 DAU 쪼개보기 - 듀오링고의 그로스 모델을 소개합니다.

원문 : [How Duolingo Reignited User Growth](https://www.lennysnewsletter.com/p/how-duolingo-reignited-user-growth)

## 배경
- 듀오링고는 A/B 테스트에는 강했지만, 전사적 전략 수립에 데이터 인사이트를 잘 활용하지 못하고 있었다.
- 따라서, DAU 성장에 실질적으로 기여할 수 있는 North Star Metric을 설정하기로 했다.

### 사용자 세분화 모델 구성
- 단순해보이는 개념의 DAU를 듀오링고는 세분화해서 쪼개보았다. 
- 그 결과, 듀오링고의 모든 사용자는 하루 단위로 아래 7개 세그먼트 중 하나에 속하게 된다. 👉 MECE하게!

#### 🌟 상위 4개 ‘활성’ 버킷 (DAU 구성요소)

> DAU = New + Current + Reactivated + Resurrected

| 세그먼트              | 정의                                 |
|------------------|----------------------------------------------|
| New users           | 앱 사용 첫날                          |
| Current users (CURR)| 최근 6일 중 오늘 포함 2회 이상 사용   |
| Reactivated users   | 7~29일 비활성 → 오늘 재활성           |
| Resurrected users   | 30일 이상 비활성 → 오늘 재활성        |

#### 🌒 하위 3개 ‘비활성’ 버킷 (WAU/MAU 차감 요소)

> WAU = DAU + At-risk WAU
<br> MAU = WAU + At-risk MAU
<br> MAU + dormant users =  Total user base

| 세그먼트         | 정의                                         |
|------------------|----------------------------------------------|
| At-risk WAU      | 오늘은 비활성, 최근 6일 중 사용 기록 있음     |
| At-risk MAU      | 최근 7일은 비활성, 이전 23일 이내 사용 있음       |
| Dormant users    | 최근 31일 이상 비활성                        |


## 지표 전환 흐름 정의 (Retention Arrows)
그 후, 위와 같이 나눈 각 사용자 버킷의 이동 확률로 일일 전환율로 재정의했다. 이를 통해 각 사용자 세그먼트가 어떤 범주의 리텐션 그룹에 속하는지 한 눈에 확인할 수 있게 되었다.

- Current users retention rate (CURR): 지난 2주 동안 제품을 방문했던 사용자가 이번 주에 다시 방문할 확률
- New users retention rate (NURR): 지난 주에 제품을 처음 방문한 사용자가 이번 주에 다시 방문할 확률
- Reactivated user retention rates (RURR): 지난 주에 다시 활성화한 사용자가 이번 주에 다시 돌아올 확률
- Resurrected user retention rate (SURR): 지난주에 (장기 부재에서) 부활한 사용자가 이번 주에 다시 돌아올 확률
- iWAURR: At-risk WAU의 복귀율 등

![그로스모델](/images/growth_model.webp)

## 시뮬레이션 및 민감도 분석
👉 사용자 세그먼트와 버킷 간 이동확률을 정의한 후, 어떤 버킷이 중요한지 파악하기 위해 민감도 분석을 수행했다.

- 매일 데이터 스냅샷을 찍어, 지난 몇 년 동안 일별로 사용자 버킷과 리텐션율의 변화를 정리했다.
- 이 데이터로 미래 에측 모델을 만들어 민감도 분석을 수행하여 어떤 레버가 DAU 증가에 가장 큰 영향을 미치는지 예측했다. 각 전환율을 분기당 +2%씩 향상시킨다고 가정하고, 3년간의 DAU/MAU 성장 시나리오를 시뮬레이션했다.

👉 그 결과, CURR이 DAU에 미치는 영향이 가장 컸으며 (2위 대비 5배), MAU에는 NURR, SURR이 더 큰 영향을 주어, 목적별로 전략을 분리할 필요가 있음을 알 수 있었다.
- 따라서, CURR을 North Star Metric으로 선정하고, 이를 위한 전략을 수립하게 되었고,
- 특히, 신규사용자 유지율에 집중하지 않고, 지속적인 이용자를 유지시키는 전략으로 관점을 크게 전환하게 되었다!


### 마치며
- 이 글을 통해 단순히 DAU를 관찰하는 것에서 벗어나, 이 지표를 분해해서 볼 수 있는 관점에 대해 알게 되었다. 이때, 세그먼트는 MECE하게 정의해야 추적과 시뮬레이션이 가능하며,
North Star Metric은 민감도 분석 기반의 영향력으로 결정하는 케이스도 확인할 수 있었다.


