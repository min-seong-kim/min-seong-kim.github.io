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

4. *Workload management*: SSD의 성능은 워크로드에 달려있다. 시퀀셜한 워크로드에서 좋은 성능을 보일 수 있지만 랜덤 워크로드에서는 좋지 않은 성능을 보일 수 있다.

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
즉 각 Die마다 interleaving 작업이 가능하다.   

Die 내 존재하는 Plane에서도 병렬 작업이 되기 때문에 Plane을 독립적으로 작동할 수 있다.   

Block은 NAND Flash에서 삭제 단위이며 Page는 R/W의 단위이고 크기는 4KB이다.   

* * *

<div class="SSD Parelle Architecture">
    {% include figure.liquid loading="eager" path="assets/img/Serial_Read.png" %}
</div>

Page를 Serial Read하는 과정   

1. page를 레지스터로 읽어 드리는데 25us가 걸리고

2. 레지스터의 데이터를 Data bus를 통해 SSD Controller로 옮기는데 100us가 소모된다.

3. 총 소모 시간을 합하면 125us가 소모되며 이를 대역폭으로 나타내면 초당 32MB의 성능을 보여준다.

만약 인터리빙을 적용하면 데이터를 레지스터로 전송하는 시간(25µs) 동안 다른 페이지의 데이터를컨트롤러로 전송하는 작업을 병렬로 동시에 수행할 수 있기 때문에, 전체 작업 주기가 100µs로 줄어든다.

<div class="SSD Parelle Architecture">
    {% include figure.liquid loading="eager" path="assets/img/Serial_Write.png" %}
</div>

Page를 serial Write하는 과정   

1. Controller에서 레지스터로 데이터를 가져오는데 100us가 걸리며 레지스터에서 페이지로 데이터를 Write하는데 200us가 걸린다.

2. 총 소모 시간을 합하면 300us가 소모되며 이를 대역폭으로 나타내면 초당 13MB의 성능을 보여준다.

만약 인터리빙을 적용하면 컨트롤러에서 레지스터로 데이터를 전송하는 시간(100us)동안 쓰기 작업이 병렬로 동시에 수행할 수 있기 때문에 전체 작업 주기가 200us로 줄어든다.

* * *

<div class="SSD Parelle Architecture">
    {% include figure.liquid loading="eager" path="assets/img/Interleaving.png" %}
</div>

위 그림은 두 플래시 다이에 연결된 직렬 버스 핀을 사용해 4개의 소스 플래인과 4개의 목적지 플래인 사이에서 Page 복사를 인터리빙하는 과정을 보여준다.   

한 Plane에서 데이터가 전송되는 동안 다른 Plane에서 쓰기 작업이 진행되기 때문에 매 간격 즉 100us동안 쓰기 작업이 완료된다.   

이런 모습을 통해 동일 package에서는 다중 Read/Write이 좋은 성능을 보이는 것을 알 수 있다.   

이렇게 복사된 데이터들은 인터리빙된 패키지 간 복사와 유사한 성능을 보이지만 직렬 핀을 사용하지 않아 더 빠른 성능을 보여준다.   

# SSD Basics

<div class="SSD Parelle Architecture">
    {% include figure.liquid loading="eager" path="assets/img/ssd_arch_2.png" %}
</div>

SSD의 전반적인 구조를 설명한다.   
먼저 Host Interface logic에서는 USB나 SATA, PCLE 같은 물리 호스트 인터페이스와의 연결과 SSD의 FTL 기능을 지원하는 logical disk emulation를 지원한다.   

Buffer Manager에서는 보류 중이거나 유효한 요청들을 보관하는 역할을 하며 Multiplexer는 명령을 처리하고 serial bus를 따라 flash package에 대한 데이터 전송을 처리한다.   

Process는 명령의 흐름과 LBA에서 PBA로의 매핑을 관리한다.

Mux는 여러 개의 입력 신호를 하나의 출력 신호로 결합, 즉 여러 패키지에서 데이터를 읽어올 때 이 데이터를 통합해 컨트롤러로 전송한다.

Demux는 하나의 입력 신호를 여러 개의 입력 신호로 분할하는 역할이며 컨트롤러에서 보내는 데이터를 각각의 패키지로 분배해 병렬로 데이터를 작업한다.

### Allocation pool

SSD에서는 in place update가 불가능하고 load balance와 wear-leveling을 위해 LBA와 PBA간 매핑을 유지해야 한다.   
그렇기 위해 쓰기 요청이 들어오면 어떤 블록을 할당할 지 고려하기 위해 Allocation pool의 추상화를 통해 mapping을 구성한다.   
쓰기 요청을 처리할 때 들어오는 Logical Page는 미리 결정된 Allocation Pool에서 할당된다.   

Static map과 dynamic map은 allocation pool에서 매핑을 정적으로 동적으로 진행한다.   
입력되는 Logical page의 size는 1KB부터 블록 크기인 256KB까지 정할 수 있으며 Logical page가 여러 패키지에 저장되어 병렬로 처리될 수 있는 Page span을 고려해야 한다.

### Three constraints for variables

`Load balancing`: I/O작업이 allocation pool에서 균등하게 이루어져야합니다.   

`Parallel access`: 여러 논리적 주소가 동시에 접근된다면, 이들을 각각 병렬로 접근할 수 있게 만들어야 한다.   

`Block erase`: flash page는 overwrite가 불가능하므로 블록을 지우고 데이터를 써야 한다.   

Allocation pool을 정의하는 변수들과 3가지 제약 조건은 trade off 존재

Static mapping ↑, load balancing ↓(병렬적으로 I/O를 처리하기 어려움)   
Span in same block -> parallel access ↓   
Logical Page size ↓ -> 블록 내 페이지 개수 증가 GC 작업 시 더 많은 valid page를 식별하기 위해 데이터를 이동하는 작업량 증가   
Logicla Page size ↑(block size) -> 쓰기와 삭제 단위 같아서 삭제가 단순함   
하지만 logical page 보다 작은 크기의 쓰기는 read – modify – write로 이어짐   



