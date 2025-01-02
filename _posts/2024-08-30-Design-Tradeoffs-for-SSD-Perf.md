---
layout: post
title: Design Tradeoffs for SSD Performance
date: 2024-09-15 00:00:00
description: This paper explores the various tradeoffs in solid state drive design
tags: SSD
categories: 
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

여름방학 FTL 스터디 때 공부한 SSD에 관한 논문인 Design Tradeoffs for SSD Performance에 대한 내용

<br>

# Introduction

<div style="display: flex; gap: 10px; justify-content: center;">
  <div class="HDD" style="flex: 1;">
    {% include figure.liquid loading="eager" path="assets/img/HDD.jpg" %}
  </div>
  <div class="SSD" style="flex: 1;">
    {% include figure.liquid loading="eager" path="assets/img/SSD.jpg" %}
  </div>
</div>

NAND Flash 기반 SSD는 기존에 사용한 HDD 보다 뛰어난 성능을 보여 컴퓨터 스토리지 구조에 큰 영향을 미쳤다.   

`HDD`: 바이트 당 싼 가격, 더 큰 용량, 오래 사용 가능한 수명
`SSD`: HDD보다 뛰어난 대역폭, 속도, 랜덤 I/O 성능, 전력 절감, 내구성

* * *

SSD Design하면서 고려해야 할 특징들이 존재한다.

1. *Data placement*: load balancing과 wear-leveling을 고려하여 SSD chip에 데이터를 위치시켜야 한다.

2. *Parallelism*: 특정 플래시 칩만으로는 최적의 성능을 낼 수 없기에 메모리 구성 요소들을 병렬로 작동시켜야 한다.

3. *Write Ordering*: NAND flash는 page 단위로 읽고 쓰고 block 단위로 삭제하기 때문에 삭제나 쓰기 작업 시 WAF(write amplification)가 발생할 수 있다. 때문에 쓰기 순서가 랜덤일 경우 성능 저하를 방지하기 위해 쓰기 순서를 효율적으로 관리해야 한다.

4. *Workload management*: SSD의 성능은 워크로드에 달려있다. 시퀀셜한 워크로드에서 좋은 성능을 보일 수 있지만 랜덤 워크로드에서는 나쁜 성능을 보일 수 있다.

# Background

<div class="SSD Architecture">
    {% include figure.liquid loading="eager" path="assets/img/ssd_arch.png" %}
</div>

* * *

<div class="SSD Parelle Architecture">
    {% include figure.liquid loading="eager" path="assets/img/ssd_paralle.png" %}
</div>


해당 모델은 2007년에 삼성에서 나온 NAND Flash 모델 K9XXG08UXM 이다.   

Single level cell(SLC) Flash 모델이며 다른 MLC TLC보다 성능과 수명이 길다.   

Flash Package의 크기는 4GB이고 Package마다 하나 이상의 다이를 가진다.   
여러 패키지를 병렬로 사용해 데이터 처리 속도를 늘릴 수 있다.   

Die는 Package 내에서 독립적으로 작동하는 단위로 여러 Plane으로 구성된다.   
각 Die는 칩 선택 라인과 Ready/Busy signal을 가지므로 한 Die가 Command를 받을 때 다른 Die는 Data 작업을 할 수 있다.   
즉 각 Die마다 interleaving 작업이 가능합니다.   

Die 내 존재하는 Plane에서도 병렬 작업이 되기 때문에 Plane을 독립적으로 작동할 수 있다.   
