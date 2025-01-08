---
layout: post
title: Machine Learning(1)
date: 2025-01-08 00:00:00
description: This is about the machine learning study Hail(human+artificial intelligence lab).
tags: Machine_learning 
categories: winter_study
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

2025 겨울방학 Hail 스터디에서 배운 MLDL에 관한 내용   
+   
3학년 2학기 딥러닝 강의 때 배운 내용   


# Machine learning 

기계 학습은 일반적인 프로그래밍 방식과 다르다.   

일반적인 프로그래밍 방식은 Data를 미리 코딩된 프로그램에 넣어 computation을 통해 output을 출력하지만 
기계 학습은 Training(정답인 input과 output set을 학습시킴)을 통해 만든 Program(aka "Model")에 새로운 데이터를 넣었을 때 적절한 output을 출력한다.





기계 학습은 크게 3가지로 분류된다.

- Supervised learning(지도 학습)
- Unsupervised learning(비지도 학습)
- Reinforcement learning(강화 학습)   

이전 딥러닝을 배우면서 지도 학습을 배웠고 이후 스터디에서 강화 학습에 대해 자세히 다루기 때문에 공부해보지 못한 비지도 학습을 더 깊게 공부해 보겠다.

<br>

## Supervised learning

지도 학습은 데이터의 input과 output을 아는 상태에서 둘 사이의 관계를 유형적으로 학습한다.   

#### Classification

분류는 각 데이터가 어떤 class에 속하는지 구분하는 작업이다.   
정답값이 categorial한 변수라고 할 수 있다.


**$$\hat{y} = \hat{f}(\mathbf{x})$$**


`x`: input으로 임의의 다차원 데이터(예: 사진, 텍스트)   
`y`: output으로 categorial한 데이터(예: 강아지, 고양이)   
$$\hat{f}$$: 수학적으로 input x를 $\hat{y}$로 예측, 기계 학습을 가지고 학습시키고자 하는 모델  

<br>

$$\mathcal{D} = \{(\mathbf{x}_i, y_i)\}_{i=1}^N$$ 

<br>

즉 분류는 위 input-output 데이터 셋을 Traning하여 $$y = \hat{y}$$ 가 되도록 $$\hat{f}$$를 찾아내는 과정이다.

<br>

#### Regression

회귀는 각 데이터가 어떤 continuous variable에 가까운지 예측하는 작업이다.   
예측하기 때문에 정답값이 continuous한 변수라고 할 수 있다.

**$$\hat{y} = \hat{f}(\mathbf{x})$$**   

`x`: input으로 임의의 다차원 데이터(예: 자동차의 다양한 정보)   
`y`: output으로 continuos한 데이터(예: 자동차의 출력, 가격)   
$$\hat{f}$$: 수학적으로 input x를 $\hat{y}$로 예측, 기계 학습을 가지고 학습시키고자 하는 모델   

**$$\mathcal{D} = \{(\mathbf{x}_i, y_i)\}_{i=1}^N$$ input-output**   
회귀는 위 데이터 셋을 Traning하여 $$|y - \hat{y}|$$ 가 최소가 되게 하는(즉, 0이 되도록) $$\hat{f}$$를 찾아내는 과정으로 다음과 같이 요약할 수 있다.   


**$$\arg\min_{\hat{f}} (y - \hat{y})$$**   

**$$|y - \hat{y}|** 을 최소로 하는 argumnet **\hat{f}$$** 을 찾겠다.

<br>

## Unsupervised learning

비지도 학습은 데이터의 output을 모르는 상태에서 input의 흥미로운 특징(insteresting structure)을 무형적으로 찾아내는 것이다.   
입력 데이터만 제공되고 그 데이터들을 기반으로 관계를 학습한다.   

#### Clustering

군집화는 데이터를 비슷한 것 끼리 짝지어 묶는 방법이다.   

Cluster는 Euclidean 거리와 코사인 유사도 등을 사용해 기준을 나누며 Cluster 내부 데이터는 서로 유사성이 높다고 볼 수 있다.   

가장 대표적인 알고리즘은 K-Means이다.   

##### K-Means Clustering

K-Means Clustering에서 K는 Cluster의 개수이며 Means는 평균을 의미한다. 이때 평균은 Cluster의 중심지로부터 데이터들 간의 거리의 평균을 나타낸다.   
전체 과정은 5단계로 진행된다.   

* * *

###### ① 군집의 개수(K) 설정
가장 먼저 Cluster의 개수를 정하는 것이다. Cluster의 개수를 어떻게 정하는지에 따라 학습 결과가 크게 변하므로 데이터에 따라 적절하게 Cluster의 개수를 정해야 한다. 이를 정하는 방법이 몇 가지 존재한다.   

1) Rule of Thumb

이 방법은 가장 간단하고 빠른 방법으로 맨 처음 명확한 기준이 없을 경우에 사용하며 대략적으로 Cluster의 개수를 추정한다.   
하지만 데이터 구조를 반영하지 못하므로 여러 관점에서 비효율적이다.   
필요한 클러스터의 개수는 아래 식처럼 구할 수 있다.   

**$$k \approx \sqrt{\frac{n}{2}}$$**

2) Elbow Method

이 방법은 최적의 Cluster 개수 K를 시각적으로 확인하는 방법이다.   
Cluster를 늘리고 줄여가며 clustering의 결과를 최적으로 만드는 지점 엘보우(Elbow)에서 최적의 K를 찾는다.   
선형 회귀에서 간단하게 배운 최소제고법과 비슷한 SSE(Sum of Squared Errors)를 사용한다.

**$$SSE = \sum_{k=1}^{K} \sum_{x_i \in C_k} \|x_i - \mu_k\|^2$$**

$$x_i$$: Cluster $$C_k$$에 속한 데이터 포인트
$$mu_k$$: Cluster $$C_k$$의 중심점

위 식을 통해 Cluster 내 데이터 포인트와 중심점 간의 거리의 제곱의 합을 구한다.   
이때 만약 K가 증가하면 Cluster가 세분화되어 각 데이터가 중심점에 가까워지므로 SSE가 감소한다.   
이때 SSE의 감소율을 살펴보면서, 즉 K가 증가할 수록 감소율이 급격히 줄어드는 지점인 엘보우를 찾아 그 때의 K를 최적의 Cluster 개수로 간주한다.   
이 방법은 SSE 함수를 통해 간단하고 직관적으로 최적의 Cluster 개수를 찾을 수 있지만 엘보우의 값을 구하면서 미분 시 후보 지점이 여러 개가 되거나 과적합이 생길 수 있으므로 추가적인 조치가 필요하다.   

3) Information Criterion Approach

이 방식은 Cluster 모델의 적합성을 통계적으로 측정하는 방법으로 모델 복잡성(Complexity)과 데이터 적합성(fit) 간의 Trade-Off을 고려해 최적의 Cluster 개수 K를 구한다.   
Cluster 모델이 데이터를 잘 설명하려면 복잡한 구조가 필요하지만 과도하게 복잡해지면 과적합이 발생할 수 있다.   
따라서 다음 두 항목을 평가한다.   
`적합성(Fit)`: 모델이 데이터를 얼마나 잘 설명하는지   
`간결성(simplicity)`: 모델의 복잡성을 패널티로 추가해 과적합 방지

<br>

(딥러닝 때 배운 과적합 방지 방법인 Weight decay과 비슷한 것 같다)

평가 방법은 2가지로 아래와 같다.
1. AIC(Akaike Information Criterion)

**$$AIC = -2 \cdot \log L + 2 \cdot p$$**

- 모델의 log-likelihood와 모델의 복잡성 간의 균형을 평가   
- 값이 작을수록 더 적합한 모델
- K가 증가하면 모델의 log-likelihood도 증가하지만 모델 복잡성 패널티(2 ⋅ p)을 통해 과적합을 방지한다.

2. BIC (Bayesian Information Criterion)

**$$BIC = -2 \cdot \log L + \log(n) \cdot p$$**

- AIC 방식보다 데이터 샘플 크기(n)에 따른 더 큰 복잡성 패널티 부여
- BIC 값이 작을수록 더 나은 모델
- BIC는 AIC보다 간단한 모델을 선호(샘플 크기 𝑛이 클수록 복잡성 패널티가 커지기 때문)

* * *

###### ② 초기 중심점 설정



* * *

###### ③ 데이터를 Cluster에 할당



* * *

###### ④ Cluster 중심점 재설정



* * *

###### ⑤ 데이터를 Cluster에 재할당(중심점이 고정될 때가지 ④,⑤를 반복)


* * *

## Reinforcement learning

위 두 가지 학습들은 주어진 데이터를 분석하고 학습하지만 강화 학습의 경우 직접 환경을 돌아다니며 학습한다.