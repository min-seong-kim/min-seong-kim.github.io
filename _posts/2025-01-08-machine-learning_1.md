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

<br>

**$$\mathcal{D} = \{(\mathbf{x}_i, y_i)\}_{i=1}^N$$ input-output**  

<br>

회귀는 위 데이터 셋을 Traning하여 $$|y - \hat{y}|$$ 가 최소가 되게 하는(즉, 0이 되도록) $$\hat{f}$$를 찾아내는 과정으로 다음과 같이 요약할 수 있다.   


**$$\arg\min_{\hat{f}} (y - \hat{y})$$**   

**$$|y - \hat{y}|** 을 최소로 하는 argumnet **\hat{f}$$** 을 찾겠다.

<br>

## Unsupervised learning

비지도 학습은 데이터의 output을 모르는 상태에서 input의 흥미로운 특징(insteresting structure)을 무형적으로 찾아내는 것이다.   
입력 데이터만 제공되고 그 데이터들을 기반으로 관계를 학습한다.   

#### **Clustering**

군집화는 데이터를 비슷한 것 끼리 짝지어 묶는 방법이다.   

Cluster는 Euclidean 거리와 코사인 유사도 등을 사용해 기준을 나누며 Cluster 내부 데이터는 서로 유사성이 높다고 볼 수 있다.   

가장 대표적인 알고리즘은 K-Means이다.   

##### *K-Means Clustering*

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

<br>

2) Elbow Method

이 방법은 최적의 Cluster 개수 K를 시각적으로 확인하는 방법이다.   
Cluster를 늘리고 줄여가며 clustering의 결과를 최적으로 만드는 지점 엘보우(Elbow)에서 최적의 K를 찾는다.   
선형 회귀에서 간단하게 배운 최소제고법과 비슷한 SSE(Sum of Squared Errors)를 사용한다.

<br>

**$$SSE = \sum_{k=1}^{K} \sum_{x_i \in C_k} \|x_i - \mu_k\|^2$$**

<br>

$$x_i$$: Cluster: $$C_k$$에 속한 데이터 포인트   
$$mu_k$$: Cluster: $$C_k$$의 중심점   

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

(딥러닝 때 배운 과적합 방지 방법인 Weight decay과 비슷한 것 같다)

평가 방법은 2가지로 아래와 같다.
*AIC(Akaike Information Criterion)*

**$$AIC = -2 \cdot \log L + 2 \cdot p$$**

- 모델의 log-likelihood와 모델의 복잡성 간의 균형을 평가   
- 값이 작을수록 더 적합한 모델
- K가 증가하면 모델의 log-likelihood도 증가하지만 모델 복잡성 패널티(2 ⋅ p)을 통해 과적합을 방지한다.

*BIC (Bayesian Information Criterion)*

**$$BIC = -2 \cdot \log L + \log(n) \cdot p$$**

- AIC 방식보다 데이터 샘플 크기(n)에 따른 더 큰 복잡성 패널티 부여
- BIC 값이 작을수록 더 나은 모델
- BIC는 AIC보다 간단한 모델을 선호(샘플 크기 𝑛이 클수록 복잡성 패널티가 커지기 때문)

* * *

###### ② 초기 중심점 설정

1) Randomly select

가장 기본적으로 무작위 방식을 통해 초기 중심점을 설정하는 방법이다.   

먼저 입력 데이터 **$$X = \{ x_1, x_2, \dots, x_n \}$$** 와 Cluster 개수 **K** 정의한다.

이 데이터 집합 X에서 K개의 데이터 포인트를 무작위로 설정한다.   
선택된 K개의 데이터 포인터는 초기 중심점 $$\mu_1, \mu_2, \dots, \mu_K$$ 으로 설정된다.   
이 때 중복된 데이터 포인트가 설정되면 안된다.   
이제 선택된 데이터 포인트를 초기 중심점으로 고정하고 K-Means 알고리즘의 클러스터링 과정으로 넘어간다.   
하지만 무작위로 선택되기 때문에 정확성이 낮고 지역 최저점 문제가 발생할 수 있다.   

2) K-Means++

K-Means 알고리즘에서 실제 사용되는 초기 중심점 설정 방식으로 기존 Randomly select 방식보다 더 신중하게 초기 중심점을 선택한다.   
먼저 첫 번째 중심점을 Randomly select 방식처럼 설정한다.   
그럼 첫 번째 중심점 $$\mu_k$$이 설정된다.   
이 때 각 데이터 포인트와 중심점 사이 간 거리를 계산한다. 각 데이터 포인트 $$X = \{ x_1, x_2, \dots, x_n \}$$ 에 대해 가장 가까운 이미 선택된 중심점과의 거리를 구한다.   
거리를 구하는 식은 아래와 같다.   

**$$D(x_i)^2 = \min_{1 \leq j \leq k} \| x_i - \mu_j \|^2$$**   

이제 각 데이터 포인트 $$(\x_i\)$$ 가 다음 중심점에서 선택될 확률은 $$D(x_i)^2$$ 에 비례한다.   
따라서 다음 식으로 확률을 구할 수 있다.   

**$$ P(x_i) = \frac{D(x_i)^2}{\sum_{j=1}^n D(x_j)^2} $$**   

이 확률 분포에 근거해 데이터 포인트 중 하나를 무작위로 선택해 다음 중심점 $$u_2$$ 을 설정한다.   

그러면 기존 Randomly select 방식과 다를 게 무엇인가? 라고 할 수 있지만 예시를 통해 설명해보겠다.   


**입력 데이터**:   
데이터 포인트: $$ X = \{x_1, x_2, x_3, x_4\} $$   
첫 번째 중심점: $$ \mu_1 = x_1 $$

**데이터 포인트와 기존 중심점 사이의 거리 계산**:
1. $$ D(x_1)^2 = 0 $$ (자기 자신은 중심점이므로 거리 0)
2. $$ D(x_2)^2 = 4 $$
3. $$ D(x_3)^2 = 9 $$
4. $$ D(x_4)^2 = 16 $$

**각 데이터 포인트의 확률 계산**:

**$$P(x_i) = \frac{D(x_i)^2}{\sum_{j=1}^n D(x_j)^2}$$**

<br>

$$ \sum_{j=1}^n D(x_j)^2 = 0 + 4 + 9 + 16 = 29$$
$$ P(x_1) = \frac{0}{29} = 0 $$
$$ P(x_2) = \frac{4}{29} \approx 0.138 $$
$$ P(x_3) = \frac{9}{29} \approx 0.310 $$
$$ P(x_4) = \frac{16}{29} \approx 0.552 $$

<br>

그럼 확룰 분포 구간을 나타내면   
$$x_1 : [0, 0)$$
$$x_2 : [0, 0.138)$$
$$x_3 : [0.138, 0.448)$$
$$x_4 : [0.448, 1.0)$$

<br>

0~1 사이 난수 r을 생성했을 때 나온 확률에 의해 데이터 포인트가 선택되므로 꽤 합리적이라 생각할 수 있다.   
따라서 더 나은 초기 중심점을 선택할 수 있고 Randomly select에서 발생할 수 있는 지역 최저점 문제를 완화할 수 있다.   



* * *

###### ③ 데이터를 Cluster에 할당

이제 각 데이터 포인트와 중심점 간의 거리를 계산해 그 결과 가장 가까운 중심점을 찾아 해당 중심점의 Cluster에 데이터 포인트를 배정한다.   

* * *

###### ④ Cluster 중심점 재설정

이제 해당 Cluster 내에서 더 정확한 중심점을 재설정할 것이다.   
Cluster 내 데이터 포인터들의 위치의 평균을 계산해 해당 위치를 중심점으로 재갱신한다.   
이를 모든 Cluster에 대해 반복한다.    

* * *

###### ⑤ 데이터를 Cluster에 재할당(중심점이 고정될 때가지 ④,⑤를 반복)

Cluster 내 데이터 포인트들의 위치가 더 이상 변하지 않을 때까지 위 단계들을 반복한다.   
중심점의 위치가 재갱신될 때 근처의 데이터 포인트들이 다른 Cluster의 중심점에 더 가까워 질 수도 있으므로 계속 반복해 정확한 Cluster를 구현한다.   


* * *

#### Dimensionality Reduction

##### PCA (Principal Component Analysis)






## Reinforcement learning

위 두 가지 학습들은 주어진 데이터를 분석하고 학습하지만 강화 학습의 경우 직접 환경을 돌아다니며 학습한다.