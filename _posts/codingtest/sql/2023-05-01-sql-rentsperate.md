---
layout: post
title: "[Programmers] 자동차 대여 기록에서 대여중 / 대여 가능 여부 구분하기 (MySQL)"
subtitle: Level 3
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [자동차 대여 기록에서 대여중 / 대여 가능 여부 구분하기]

### 📖 문제 설명  

<p>다음은 어느 자동차 대여 회사의 자동차 대여 기록 정보를 담은 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블입니다. <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블은 아래와 같은 구조로 되어있으며, <code>HISTORY_ID</code>, <code>CAR_ID</code>, <code>START_DATE</code>, <code>END_DATE</code> 는 각각 자동차 대여 기록 ID, 자동차 ID, 대여 시작일, 대여 종료일을 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>HISTORY_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>CAR_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>START_DATE</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
<tr>
<td>END_DATE</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블에서 2022년 10월 16일에 대여 중인 자동차인 경우 '대여중' 이라고 표시하고, 대여 중이지 않은 자동차인 경우 '대여 가능'을 표시하는 컬럼(컬럼명: <code>AVAILABILITY</code>)을 추가하여 자동차 ID와 <code>AVAILABILITY</code> 리스트를 출력하는 SQL문을 작성해주세요. 이때 반납 날짜가 2022년 10월 16일인 경우에도 '대여중'으로 표시해주시고 결과는 자동차 ID를 기준으로 내림차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블이 다음과 같다면</p>
<table class="table">
        <thead><tr>
<th>HISTORY_ID</th>
<th>CAR_ID</th>
<th>START_DATE</th>
<th>END_DATE</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>4</td>
<td>2022-09-27</td>
<td>2022-09-27</td>
</tr>
<tr>
<td>2</td>
<td>3</td>
<td>2022-10-03</td>
<td>2022-10-04</td>
</tr>
<tr>
<td>3</td>
<td>2</td>
<td>2022-10-05</td>
<td>2022-10-05</td>
</tr>
<tr>
<td>4</td>
<td>1</td>
<td>2022-10-11</td>
<td>2022-10-16</td>
</tr>
<tr>
<td>5</td>
<td>3</td>
<td>2022-10-13</td>
<td>2022-10-15</td>
</tr>
<tr>
<td>6</td>
<td>2</td>
<td>2022-10-15</td>
<td>2022-10-17</td>
</tr>
</tbody>
      </table>
<p>2022년 10월 16일에 대여 중인 자동차는 자동차 ID가 1, 2인 자동차이고, 대여 가능한 자동차는 자동차 ID가 3, 4이므로, '대여중' 또는 '대여 가능' 을 표시하는 컬럼을 추가하고, 자동차 ID를 기준으로 내림차순 정렬하면 다음과 같이 나와야 합니다.</p>
<table class="table">
        <thead><tr>
<th>CAR_ID</th>
<th>AVAILABILITY</th>
</tr>
</thead>
        <tbody><tr>
<td>4</td>
<td>대여 가능</td>
</tr>
<tr>
<td>3</td>
<td>대여 가능</td>
</tr>
<tr>
<td>2</td>
<td>대여중</td>
</tr>
<tr>
<td>1</td>
<td>대여중</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. CASE WHEN THEN 문을 이용한 '대여중', '대여 가능'을 구분
2. IN절을 이용해 서브쿼리에서 반환된 값들이 CAR_ID와 같다면 '대여중'을 별칭으로 지정, 만약 같지 않다면 '대여 가능'으로 지정
3. 시작날짜, 종료날짜를 가지고 2022-10-16일을 포함하는지 확인하기 위해서는 해당일을 기준으로 WHERE, BETWEEN 문법으로 사이에 해당하는지 필터링할 수 있다.
4. CAR_ID를 GROUP BY -> 그룹바이할때 2번으로 인해서 대여중인 ID가 하나라도 존재한다면 대여중인 상태로 반환된다.

```roomsql
SELECT
    CAR_ID,
    CASE WHEN CAR_ID IN (
        SELECT CAR_ID
        FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
        WHERE '2022-10-16' 
        BETWEEN DATE_FORMAT(START_DATE,'%Y-%m-%d')
        AND DATE_FORMAT(END_DATE,'%Y-%m-%d')
    ) THEN '대여중'
    ELSE '대여 가능'
    END AS AVAILABILITY
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY
GROUP BY CAR_ID
ORDER BY CAR_ID DESC;
```



<br/>

[자동차 대여 기록에서 대여중 / 대여 가능 여부 구분하기]:https://school.programmers.co.kr/learn/courses/30/lessons/157340
