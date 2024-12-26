---
layout: post
title: SQLite Analysis of Deleted Record Restoration Techniques
date: 2024-08-20 00:00:00
description: This is about the sqlite forensics analysis published by WDSC.
tags: SQLite forensics
categories: WDSC
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

WDSC 논문 발표를 준비하며 공부한 SQLite Forensics 내용들

## SQLite Architecture

다음 사진은 SQLite의 구조를 보여준다.
<div class="SQLite Architecture">
    {% include figure.liquid loading="eager" path="assets/img/SQLite_Architecture.png" %}
</div>

SQLite는 페이지를 기본 단위로 사용하며 여러 개의 페이지로 구성되어 있다.
`Database file header`: 데이터베이스의 전반적인 메타데이터를 저장하고 첫 페이지에만 존재
`Page header`: 페이지의 메타데이터를 저장
`Cell pointer array`: 페이지 내 각 셀의 위치를 저장
`Unallocated area`: 아직 할당되지 않은 공간
`Cell`: 실제 데이터를 저장하는 단위
`Reserved space`: 성능 최적화나 데이터 무결성을 위한 공간


## Cell Architecture

<div class="Cell Architecture">
    {% include figure.liquid loading="eager" path="assets/img/Cell_Architecture.png" %}
</div>

셀의 내부 구조를 살펴보면
`Cell header`: 셀의 크기와 레코드를 식별하는 Row ID를 저장
`Record header`: 헤더의 크기와 각 데이터 필드의 타입과 길이를 저장
`Data area`: 실제 데이터가 저장



## Deleted record recovery technique

### Free page list analysis

<div class="Cell Architecture">
    {% include figure.liquid loading="eager" path="assets/img/free_page_list.png" %}
</div>

Database 파일의 모든 프리 페이지는 리스트로 서로 연결되어 있고 삭제된 데이터를 포함하고 있다. 이 프리 페이지들을 분석하여 삭제된 레코드를 복원할 수 있다.




## Customizing Your Table of Contents

{:data-toc-text="Customizing"}

If you want to learn more about how to customize the table of contents of your sidebar, you can check the [bootstrap-toc](https://afeld.github.io/bootstrap-toc/) documentation. Notice that you can even customize the text of the heading that will be displayed on the sidebar.

### Example of Sub-Heading 2

Jean shorts raw denim Vice normcore, art party High Life PBR skateboard stumptown vinyl kitsch. Four loko meh 8-bit, tousled banh mi tilde forage Schlitz dreamcatcher twee 3 wolf moon. Chambray asymmetrical paleo salvia, sartorial umami four loko master cleanse drinking vinegar brunch. <a href="https://www.pinterest.com">Pinterest</a> DIY authentic Schlitz, hoodie Intelligentsia butcher trust fund brunch shabby chic Kickstarter forage flexitarian. Direct trade <a href="https://en.wikipedia.org/wiki/Cold-pressed_juice">cold-pressed</a> meggings stumptown plaid, pop-up taxidermy. Hoodie XOXO fingerstache scenester Echo Park. Plaid ugh Wes Anderson, freegan pug selvage fanny pack leggings pickled food truck DIY irony Banksy.

### Example of another Sub-Heading 2

Jean shorts raw denim Vice normcore, art party High Life PBR skateboard stumptown vinyl kitsch. Four loko meh 8-bit, tousled banh mi tilde forage Schlitz dreamcatcher twee 3 wolf moon. Chambray asymmetrical paleo salvia, sartorial umami four loko master cleanse drinking vinegar brunch. <a href="https://www.pinterest.com">Pinterest</a> DIY authentic Schlitz, hoodie Intelligentsia butcher trust fund brunch shabby chic Kickstarter forage flexitarian. Direct trade <a href="https://en.wikipedia.org/wiki/Cold-pressed_juice">cold-pressed</a> meggings stumptown plaid, pop-up taxidermy. Hoodie XOXO fingerstache scenester Echo Park. Plaid ugh Wes Anderson, freegan pug selvage fanny pack leggings pickled food truck DIY irony Banksy.
