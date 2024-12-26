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

<br>

## SQLite Architecture

다음 사진은 SQLite의 구조를 보여준다.
<div class="SQLite Architecture">
    {% include figure.liquid loading="eager" path="assets/img/SQLite_Architecture.png" %}
</div>

<br>

SQLite는 페이지를 기본 단위로 사용하며 여러 개의 페이지로 구성되어 있다.
`Database file header`: 데이터베이스의 전반적인 메타데이터를 저장하고 첫 페이지에만 존재
`Page header`: 페이지의 메타데이터를 저장
`Cell pointer array`: 페이지 내 각 셀의 위치를 저장
`Unallocated area`: 아직 할당되지 않은 공간
`Cell`: 실제 데이터를 저장하는 단위
`Reserved space`: 성능 최적화나 데이터 무결성을 위한 공간  



<br><br>


## Cell Architecture

<div class="Cell Architecture">
    {% include figure.liquid loading="eager" path="assets/img/Cell_Architecture.png" %}
</div>

<br>

셀의 내부 구조를 살펴보면
`Cell header`: 셀의 크기와 레코드를 식별하는 Row ID를 저장
`Record header`: 헤더의 크기와 각 데이터 필드의 타입과 길이를 저장
`Data area`: 실제 데이터가 저장  

<br><br>


## Deleted record recovery technique
<br>

### Free page list analysis

<div class="Cell Architecture" style="max-width: 50%; margin: auto;">
    {% include figure.liquid loading="eager" path="assets/img/free_page_list.png" %}
</div>

<br>

Database 파일의 모든 프리 페이지는 리스트로 서로 연결되어 있고 삭제된 데이터를 포함하고 있다. 이 프리 페이지들을 분석하여 삭제된 레코드를 복원할 수 있다.  


아래는 삽입한 레코드이다.
<br>
| Name | Affiliation              | StudentID |
|------|--------------------------|-----------|
| KMS  | Dankook University1      | 1         |
| LSH  | Dankook University2      | 2         |
| ...  | ...                      | ...       |
| KVY  | Dankook University2999   | 2999      |
| LYM  | Dankook University3000   | 3000      |

<br>

삭제된 데이터 흔적이 남아 있기 위해 SQLite 기능인 secure_delete를 비활성화하고 삭제 작업 진행

<br>

```sqlite> PRAGMA secure_delete = 0;```

<br>

#### Database file header analysis 

아래 표는 Database file header에서 첫 40Byte의 구조를 보여준다.

| Offset | Size | Description                                  |
|--------|------|----------------------------------------------|
| 0      | 16   | Header string                               |
| 16     | 2    | Page size                                   |
| 18     | 1    | File format **write** version               |
| 19     | 1    | File format **read** version                |
| 20     | 1    | Size of reserved space                      |
| 21     | 1    | Maximum payload fraction                    |
| 22     | 1    | Minimum payload fraction                    |
| 23     | 1    | Leaf payload fraction                       |
| 24     | 4    | File change counter                         |
| 28     | 4    | Size of database file in pages              |
| 32     | 4    | Page number of the first free list trunk page |
| 36     | 4    | Total number of free list pages             |

<br>

이 표를 기반으로 .db 파일의 파일 헤더를 분석해보면  

<br>

```$hd test.db```  

<div class="sql file Architecture">
    {% include figure.liquid loading="eager" path="assets/img/sql_file_header.png" %}
</div>

<br>

페이지의 크기, 첫번째 free list page의 페이지 번호, 전체 free list page의 개수를 알 수 있다.

`Address = (offset32 – 1) x 0x1000`

위 식을 통해 free list page(0x5000)으로 이동  

<br><br>

#### Free page header analysis 

아래 표는 Database file header에서 첫 40Byte의 구조를 보여준다.  

<br>

| Offset | Size | Description                                 |
|--------|------|---------------------------------------------|
| 0      | 1    | B-tree page type                           |
| 1      | 2    | Start of the first freeblock on the page   |
| 3      | 2    | Number of cells on the page                |
| 5      | 2    | Start of the cell content area             |
| 7      | 1    | Number of fragmented free bytes            |
| 8      | 4    | Right-most pointer                         |

<br>
페이지의 헤더를 통해 페이지의 형식, 페이지 내 셀의 개수, 첫 셀의 시작 주소 offse을 알 수 있다.

<div class="free page header Architecture">
    {% include figure.liquid loading="eager" path="assets/img/free_page_header_code.png" %}
</div>

<br>
현재 주소인 0x5000에 시작 offse인 0x010c를 더하여 셀로 이동

<br><br>

#### Record recovery

<div class="free page header Architecture">
    {% include figure.liquid loading="eager" path="assets/img/cell_code.png" %}
</div>

<br>

셀의 위치로 이동하면 각 셀의 헤더, 레코드 헤더, 데이터 영역을 확인할 수 있다.

<div class="free page header Architecture">
    {% include figure.liquid loading="eager" path="assets/img/cell_code_2.png" %}
</div>

셀 헤더를 통해 셀의 크기를 확인할 수 있고 레코드 헤더를 통해 헤더 사이드와 데이터의 타입과 길이를 이용해 삭제된 데이터가 포함된 페이지를 복원할 수 있다.

<br>
아래 사진을 보면 실제 삽입한 studentID와 삭제된 값이 일치하는 모습을 확인할 수 있다.

<div class="free page header Architecture">
    {% include figure.liquid loading="eager" path="assets/img/cell_code_3.png" %}
</div>

### Artifact carving

<div class="free page header Architecture">
    {% include figure.liquid loading="eager" path="assets/img/ac_1.png" %}
</div>



만약 프리 페이지 리스트 복원 방식에서 프리 페이지 리스트가 없거나 덮어 씌어진 경우 파일 곳곳에 저장된 slack space를 탐색해야 한다.
두 번째 복원 방식인 아티팩트 카빙은 파일 내 모든 공간을 검사하여 복원을 진행하는 방식이다.
이 기법은 데이터 베이스 파일 헤더와 레코드 헤더의 정보를 통해 삭제된 데이터에 대한 정보를 찾기 때문에 이 부분들이 덮어씌워진다면 패턴을 유추하는데 어려워 복구가 힘들다.

<br>

<div class="free page header Architecture">
    {% include figure.liquid loading="eager" path="assets/img/ac_2.png" %}
</div>


하지만 문제가 생길 수 있는 시나리오가 있다.
기존 테이블을 삭제 후 같은 형식의 새로운 테이블을 생성하고 레코드를 삽입했다.
이때 새로 삽입된 데이터들이 기존 삭제된 데이터를 덮어 씌어진 것을 확인할 수 있다.
해당 시나리오는 아티팩트 카빙 작업을 통해 복원이 가능하나 파일 헤더 또는 레코드 헤더가 덮어씌어진 경우 정상적인 복원이 어렵다.

