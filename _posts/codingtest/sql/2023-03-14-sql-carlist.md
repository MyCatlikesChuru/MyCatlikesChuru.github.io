---
layout: post
title: "[Programmers] 특정 옵션이 포함된 자동차 리스트 구하기 (MySQL)"
subtitle: Level 1
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [특정 옵션이 포함된 자동차 리스트 구하기] 

### 📖 문제 설명  

<p>다음은 어느 자동차 대여 회사에서 대여중인 자동차들의 정보를 담은 <code>CAR_RENTAL_COMPANY_CAR</code> 테이블입니다. <code>CAR_RENTAL_COMPANY_CAR</code> 테이블은 아래와 같은 구조로 되어있으며, <code>CAR_ID</code>, <code>CAR_TYPE</code>, <code>DAILY_FEE</code>, <code>OPTIONS</code> 는 각각 자동차 ID, 자동차 종류, 일일 대여 요금(원), 자동차 옵션 리스트를 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>CAR_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>CAR_TYPE</td>
<td>VARCHAR(255)</td>
<td>FALSE</td>
</tr>
<tr>
<td>DAILY_FEE</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>OPTIONS</td>
<td>VARCHAR(255)</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<p>자동차 종류는 '세단', 'SUV', '승합차', '트럭', '리무진' 이 있습니다. 자동차 옵션 리스트는 콤마(',')로 구분된 키워드 리스트(옵션 리스트 값 예시: '열선시트', '스마트키', '주차감지센서')로 되어있으며, 키워드 종류는 '주차감지센서', '스마트키', '네비게이션', '통풍시트', '열선시트', '후방카메라', '가죽시트' 가 있습니다.</p>

<hr>

<h5>문제</h5>

<p><code>CAR_RENTAL_COMPANY_CAR</code> 테이블에서 '네비게이션' 옵션이 포함된 자동차 리스트를 출력하는 SQL문을 작성해주세요. 결과는 자동차 ID를 기준으로 내림차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>CAR_RENTAL_COMPANY_CAR</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>CAR_ID</th>
<th>CAR_TYPE</th>
<th>DAILY_FEE</th>
<th>OPTIONS</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>세단</td>
<td>16000</td>
<td>가죽시트,열선시트,후방카메라</td>
</tr>
<tr>
<td>2</td>
<td>SUV</td>
<td>14000</td>
<td>스마트키,네비게이션,열선시트</td>
</tr>
<tr>
<td>3</td>
<td>SUV</td>
<td>22000</td>
<td>주차감지센서,후방카메라,네비게이션</td>
</tr>
</tbody>
      </table>
<p>'네비게이션' 옵션이 포함된 자동차는 자동차 ID가 2, 3인 자동차이고, 자동차 ID를 기준으로 내림차순 정렬하면 다음과 같은 결과가 나와야 합니다.</p>
<table class="table">
        <thead><tr>
<th>CAR_ID</th>
<th>CAR_TYPE</th>
<th>DAILY_FEE</th>
<th>OPTIONS</th>
</tr>
</thead>
        <tbody><tr>
<td>3</td>
<td>SUV</td>
<td>22000</td>
<td>주차감지센서,후방카메라,네비게이션</td>
</tr>
<tr>
<td>2</td>
<td>SUV</td>
<td>14000</td>
<td>스마트키,네비게이션,열선시트</td>
</tr>
</tbody>
      </table>

---

## 🗝 문제 풀이

OPTIONS 칼럼의 네비게이션이라고 되어있는 단어를 포함한   
칼럼들을 찾아 ID로 내림차순으로 표현해주는 문제이다.

```RoomSql
SELECT CAR_ID, CAR_TYPE, DAILY_FEE, OPTIONS 
FROM CAR_RENTAL_COMPANY_CAR
WHERE OPTIONS LIKE '%네비게이션%'
ORDER BY CAR_ID DESC;
```

LIKE 문법과 와일드카드의 이해를 알면 간단하게 풀리는 문제이다.

|  WILDCARD  | 설명                      |
|:----------:|-------------------------|
|     %      | 0개 이상의 문자를 대신 표현할 수 있음. |
|     _      | 1개의 문자를 표현할 수 있음.       |

**% 와일드카드의 예제**
- %m (앞에 어떤글자가 오든 맨마지막이 m으로 끝나는 문제)  
  -> ex) ham, storm
- m% (맨처음에 시작하는 문자열이 m이되는 모든 문자열)   
  -> ex) mama, monster
- %m% (문자열 중간에 m이 들어간 모든 문자)   
  -> ex) aaamaaa, aamaaa, m

<br/>

**_ 와일드카드의 예제**
- _012 (4개의 문자열에서 첫번째는 모든문자열허용, 2~4는 012로 고정인 값)  
  -> ex) t012, m012, 1012 , 44012(오답)

<br/>

[특정 옵션이 포함된 자동차 리스트 구하기]:https://school.programmers.co.kr/learn/courses/30/lessons/157343
