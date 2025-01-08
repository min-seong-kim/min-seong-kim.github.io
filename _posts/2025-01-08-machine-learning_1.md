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
`$\hat{f}$`: 수학적으로 input x를 $\hat{y}$로 예측, 기계 학습을 가지고 학습시키고자 하는 모델

$\mathcal{D} = \{(\mathbf{x}_i, y_i)\}_{i=1}^N$ input-output 데이터 셋을 통해 Traning하여 $y = \hat{y}$ 가 되도록 $\hat{f}$를 찾아내는 과정이다.

<br>

#### Regression

회귀는 각 데이터가 어떤 continuous variable에 가까운지 예측하는 작업이다.   
예측하기 때문에 정답값이 continuous한 변수라고 할 수 있다.

**$\hat{y} = \hat{f}(\mathbf{x})$**   

`x`: input으로 임의의 다차원 데이터(예: 자동차의 다양한 정보)   
`y`: output으로 continuos한 데이터(예: 자동차의 출력, 가격)   
` $\hat{f}$ `: 수학적으로 input x를 $\hat{y}$로 예측, 기계 학습을 가지고 학습시키고자 하는 모델   

**$\mathcal{D} = \{(\mathbf{x}_i, y_i)\}_{i=1}^N$ input-output**   
위 데이터 셋을 통해 Traning하여 $|y = \hat{y}|$ 가 최소가 되도록 $\hat{f}$를 찾아내는 과정으로 다음과 같이 요약할 수 있다.   


**$\arg\min_{\hat{f}} (y - \hat{y})$**   

**$y - \hat{y} 을 최소로 하는 argumnet \hat{f}$ 을 찾겠다.**

<br>

## Unsupervised learning

비지도 학습은 데이터의 output을 모르는 상태에서 input의 흥미로운 특징(insteresting structure)을 무형적으로 찾아내는 것이다. 



## Reinforcement learning

위 두 가지 학습들은 주어진 데이터를 분석하고 학습하지만 강화 학습의 경우 직접 환경을 돌아다니며 학습한다.