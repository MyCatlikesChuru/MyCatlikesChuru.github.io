---
layout: post
title: "[Programmers] 자동차 대여 기록 별 대여 금액 구하기 (MySQL)"
subtitle: Level 4
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [자동차 대여 기록 별 대여 금액 구하기]

### 📖 문제 설명  

<p>다음은 어느 자동차 대여 회사에서 대여 중인 자동차들의 정보를 담은 <code>CAR_RENTAL_COMPANY_CAR</code> 테이블과 자동차 대여 기록 정보를 담은 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블과 자동차 종류 별 대여 기간 종류 별 할인 정책 정보를 담은 <code>CAR_RENTAL_COMPANY_DISCOUNT_PLAN</code> 테이블 입니다.</p>

<p><code>CAR_RENTAL_COMPANY_CAR</code> 테이블은 아래와 같은 구조로 되어있으며, <code>CAR_ID</code>, <code>CAR_TYPE</code>, <code>DAILY_FEE</code>, <code>OPTIONS</code> 는 각각 자동차 ID, 자동차 종류, 일일 대여 요금(원), 자동차 옵션 리스트를 나타냅니다.</p>
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
<p>자동차 종류는 '세단', 'SUV', '승합차', '트럭', '리무진' 이 있습니다. 자동차 옵션 리스트는 콤마(',')로 구분된 키워드 리스트(예: ''열선시트,스마트키,주차감지센서'')로 되어있으며, 키워드 종류는 '주차감지센서', '스마트키', '네비게이션', '통풍시트', '열선시트', '후방카메라', '가죽시트' 가 있습니다.</p>

<p><code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블은 아래와 같은 구조로 되어있으며, <code>HISTORY_ID</code>, <code>CAR_ID</code>, <code>START_DATE</code>, <code>END_DATE</code> 는 각각 자동차 대여 기록 ID, 자동차 ID, 대여 시작일, 대여 종료일을 나타냅니다.</p>
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
<p><code>CAR_RENTAL_COMPANY_DISCOUNT_PLAN</code> 테이블은 아래와 같은 구조로 되어있으며, <code>PLAN_ID</code>, <code>CAR_TYPE</code>, <code>DURATION_TYPE</code>, <code>DISCOUNT_RATE</code> 는 각각 요금 할인 정책 ID, 자동차 종류, 대여 기간 종류, 할인율(%)을 나타냅니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>PLAN_ID</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
<tr>
<td>CAR_TYPE</td>
<td>VARCHAR(255)</td>
<td>FALSE</td>
</tr>
<tr>
<td>DURATION_TYPE</td>
<td>VARCHAR(255)</td>
<td>FALSE</td>
</tr>
<tr>
<td>DISCOUNT_RATE</td>
<td>INTEGER</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<p>할인율이 적용되는 대여 기간 종류로는 '7일 이상' (대여 기간이 7일 이상 30일 미만인 경우), '30일 이상' (대여 기간이 30일 이상 90일 미만인 경우), '90일 이상' (대여 기간이 90일 이상인 경우) 이 있습니다. 대여 기간이 7일 미만인 경우 할인정책이 없습니다.</p>

<hr>

<h5>문제</h5>

<p><code>CAR_RENTAL_COMPANY_CAR</code> 테이블과 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블과 <code>CAR_RENTAL_COMPANY_DISCOUNT_PLAN</code> 테이블에서 자동차 종류가 '트럭'인 자동차의 대여 기록에 대해서 대여 기록 별로 대여 금액(컬럼명: <code>FEE</code>)을 구하여 대여 기록 ID와 대여 금액 리스트를 출력하는 SQL문을 작성해주세요. 결과는 대여 금액을 기준으로 내림차순 정렬하고, 대여 금액이 같은 경우 대여 기록 ID를 기준으로 내림차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>CAR_RENTAL_COMPANY_CAR</code> 테이블과 <code>CAR_RENTAL_COMPANY_RENTAL_HISTORY</code> 테이블과 <code>CAR_RENTAL_COMPANY_DISCOUNT_PLAN</code> 테이블이 다음과 같다면</p>
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
<td>트럭</td>
<td>26000</td>
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
<td>트럭</td>
<td>32000</td>
<td>주차감지센서,후방카메라,가죽시트</td>
</tr>
</tbody>
      </table><table class="table">
        <thead><tr>
<th>HISTORY_ID</th>
<th>CAR_ID</th>
<th>START_DATE</th>
<th>END_DATE</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>1</td>
<td>2022-07-27</td>
<td>2022-08-02</td>
</tr>
<tr>
<td>2</td>
<td>1</td>
<td>2022-08-03</td>
<td>2022-08-04</td>
</tr>
<tr>
<td>3</td>
<td>2</td>
<td>2022-08-05</td>
<td>2022-08-05</td>
</tr>
<tr>
<td>4</td>
<td>2</td>
<td>2022-08-09</td>
<td>2022-08-12</td>
</tr>
<tr>
<td>5</td>
<td>3</td>
<td>2022-09-16</td>
<td>2022-10-15</td>
</tr>
</tbody>
      </table><table class="table">
        <thead><tr>
<th>PLAN_ID</th>
<th>CAR_TYPE</th>
<th>DURATION_TYPE</th>
<th>DISCOUNT_RATE</th>
</tr>
</thead>
        <tbody><tr>
<td>1</td>
<td>트럭</td>
<td>7일 이상</td>
<td>5%</td>
</tr>
<tr>
<td>2</td>
<td>트럭</td>
<td>30일 이상</td>
<td>7%</td>
</tr>
<tr>
<td>3</td>
<td>트럭</td>
<td>90일 이상</td>
<td>10%</td>
</tr>
<tr>
<td>4</td>
<td>세단</td>
<td>7일 이상</td>
<td>5%</td>
</tr>
<tr>
<td>5</td>
<td>세단</td>
<td>30일 이상</td>
<td>10%</td>
</tr>
<tr>
<td>6</td>
<td>세단</td>
<td>90일 이상</td>
<td>15%</td>
</tr>
</tbody>
      </table>
<p>자동차 종류가 '트럭' 인 자동차의 대여 기록에 대해서 대여 기간을 구하면,</p>

<ul>
<li>대여 기록 ID가 1인 경우, 7일</li>
<li>대여 기록 ID가 2인 경우, 2일</li>
<li>대여 기록 ID가 5인 경우, 30일입니다.</li>
</ul>

<p>대여 기간 별로 일일 대여 요금에 알맞은 할인율을 곱하여 금액을 구하면 다음과 같습니다.</p>

<ul>
<li>대여 기록 ID가 1인 경우, 일일 대여 금액 26,000원에서 5% 할인율을 적용하고 7일을 곱하면 총 대여 금액은 172,900원</li>
<li>대여 기록 ID가 2인 경우, 일일 대여 금액 26,000원에 2일을 곱하면 총 대여 금액은 52,000원</li>
<li>대여 기록 ID가 5인 경우, 일일 대여 금액 32,000원에서 7% 할인율을 적용하고 30일을 곱하면 총 대여 금액은 892,800원이 되므로, 대여 금액을 기준으로 내림차순 정렬 및 대여 기록 ID를 기준으로 내림차순 정렬하면 다음과 같아야 합니다.</li>
</ul>
<table class="table">
        <thead><tr>
<th>HISTORY_ID</th>
<th>FEE</th>
</tr>
</thead>
        <tbody><tr>
<td>5</td>
<td>892800</td>
</tr>
<tr>
<td>1</td>
<td>172900</td>
</tr>
<tr>
<td>2</td>
<td>52000</td>
</tr>
</tbody>
      </table>
<hr>

<h5>주의사항</h5>

<p><code>FEE</code>의 경우 예시처럼 정수부분만 출력되어야 합니다.</p>


> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. (일수 * 할인율 * 대여금액) = FEE 라고 할 수 있다.
2. CASE문을 이용하여 트럭의 type별 할인율을 구할 수 있다.
3. 7일미만 = 할인 없음 / 7일이상 ~ 30일미만 = 5% / 30일이상 ~ 90일미만 = 8% / 90일 이상  = 15%
4. 3번의 할인율 비율은 CAR_RENTAL_COMPANY_DISCOUNT_PLAN 테이블을 조회해서알아낸 값이다. (즉, JOIN을 하지않고 알고있는 값으로 고정해서 적어주었다.)
5. LEFT JOIN으로 대여기록 테이블과 자동차정보 테이블을 조인한다.
6. WHERE절을 이용해 트럭으로만된 데이터를 필터링한다.
7. ORDER절을 이용해 FEE계산별로 내림차순, 같으면 HISTORY_ID 내림차순으로 정렬한다.

```roomsql
SELECT CH.HISTORY_ID,
       ROUND((DATEDIFF(CH.END_DATE,CH.START_DATE)+1) *
        (CASE
            WHEN DATEDIFF(CH.END_DATE,CH.START_DATE)+1 < 7 THEN 1
            WHEN DATEDIFF(CH.END_DATE,CH.START_DATE)+1 < 30 THEN 0.95
            WHEN DATEDIFF(CH.END_DATE,CH.START_DATE)+1 < 90 THEN 0.92
            ELSE 0.85
         END) * CC.DAILY_FEE, 0) AS FEE
FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY CH
LEFT JOIN CAR_RENTAL_COMPANY_CAR CC
ON CH.CAR_ID = CC.CAR_ID
WHERE CC.CAR_TYPE = '트럭'
ORDER BY FEE DESC, CH.HISTORY_ID DESC;
```

<br/>  

풀이방법은 위와 같이 할인율을 알고있는 상태에서 작업하였다.   
할인율은 아래와 같이 테이블을 조회해보면
```roomsql
SELECT *
FROM CAR_RENTAL_COMPANY_DISCOUNT_PLAN;
```

<table class="console-sql-result"><thead><tr><th>plan_id</th><th>car_type</th><th>duration_type</th><th>discount_rate</th></tr></thead><thead></thead><tbody><tr><td>1</td><td>세단</td><td>7일 이상</td><td>5</td></tr><tr><td>2</td><td>세단</td><td>30일 이상</td><td>8</td></tr><tr><td>3</td><td>세단</td><td>90일 이상</td><td>12</td></tr><tr><td>4</td><td>SUV</td><td>7일 이상</td><td>3</td></tr><tr><td>5</td><td>SUV</td><td>30일 이상</td><td>5</td></tr><tr><td>6</td><td>SUV</td><td>90일 이상</td><td>10</td></tr><tr><td>7</td><td>승합차</td><td>7일 이상</td><td>5</td></tr><tr><td>8</td><td>승합차</td><td>30일 이상</td><td>10</td></tr><tr><td>9</td><td>승합차</td><td>90일 이상</td><td>15</td></tr><tr><td>10</td><td>트럭</td><td>7일 이상</td><td>5</td></tr><tr><td>11</td><td>트럭</td><td>30일 이상</td><td>8</td></tr><tr><td>12</td><td>트럭</td><td>90일 이상</td><td>15</td></tr><tr><td>13</td><td>리무진</td><td>7일 이상</td><td>4</td></tr><tr><td>14</td><td>리무진</td><td>30일 이상</td><td>8</td></tr><tr><td>15</td><td>리무진</td><td>90일 이상</td><td>20</td></tr></tbody></table>

트럭의 할인율이 5%, 8%, 15%로 알 수 있다.   
위와 같은 방법으로는 해당테이블을 참조하지 않기때문에   
추후에 요구사항 변동이생긴다거나할때 대응하기 어려울 것 같다는 생각이들고   
그닥 좋은 방법이 아닌 것 같다.   

<br/>

만약 참조해서 풀어보자면 WITH 문법을 이용해 가상테이블을   
만들어 작업하는 방식도 있다.   

```roomsql
WITH SUB_DISCOUNT AS (
    SELECT
        HISTORY_ID,
        CAR_TYPE,
        DAILY_FEE*(DATEDIFF(END_DATE,START_DATE)+1) AS TOTAL_DAILY_FEE,
        CASE
        WHEN DATEDIFF(END_DATE,START_DATE)+1 < 7 THEN '할인없음'
        WHEN DATEDIFF(END_DATE,START_DATE)+1 >= 7 AND DATEDIFF(END_DATE,START_DATE)+1 < 30 THEN '7일 이상'
        WHEN DATEDIFF(END_DATE,START_DATE)+1 >= 30 AND DATEDIFF(END_DATE,START_DATE)+1 < 90 THEN '30일 이상'
        WHEN DATEDIFF(END_DATE,START_DATE)+1 >= 90 THEN '90일 이상'
        END DURATION_TYPE  
    FROM CAR_RENTAL_COMPANY_RENTAL_HISTORY AS CH
    LEFT JOIN CAR_RENTAL_COMPANY_CAR AS CC
    ON CH.CAR_ID = CC.CAR_ID
    WHERE CAR_TYPE = '트럭'
)

SELECT 
    HISTORY_ID,
    ROUND(TOTAL_DAILY_FEE * (100-IF(DISCOUNT_RATE IS NULL,0,DISCOUNT_RATE))*0.01) AS FEE
FROM SUB_DISCOUNT AS SD
LEFT JOIN CAR_RENTAL_COMPANY_DISCOUNT_PLAN AS CP
ON SD.CAR_TYPE = CP.CAR_TYPE AND SD.DURATION_TYPE = CP.DURATION_TYPE
ORDER BY FEE DESC, CH.HISTORY_ID DESC;
```
와 같은 방법으로 가상의 테이블(SUB_DISCOUNT)를 만들어    
원하는 정보의 테이블만 추출한다음   
해당 정보를 이용하는 방법으로 쿼리를 작성해볼 수 있을 것이다.



<br/>

[자동차 대여 기록 별 대여 금액 구하기]:https://school.programmers.co.kr/learn/courses/30/lessons/151141
