---
layout: post
title: "[Programmers] 인기있는 아이스크림 (MySQL)"
subtitle: Level 1
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [인기있는 아이스크림] 

### 📖 문제 설명  

<p><code>FIRST_HALF</code> 테이블은 아이스크림 가게의 상반기 주문 정보를 담은 테이블입니다.<code>FIRST_HALF</code> 테이블 구조는 다음과 같으며, <code>SHIPMENT_ID</code>, <code>FLAVOR</code>, <code>TOTAL_ORDER</code>는 각각 아이스크림 공장에서 아이스크림 가게까지의 출하 번호, 아이스크림 맛, 상반기 아이스크림 총주문량을 나타냅니다. </p>
<table class="table">
        <thead><tr>
<th style="text-align: left">NAME</th>
<th style="text-align: left">TYPE</th>
<th>NULLABLE</th>
</tr>
</thead>
        <tbody><tr>
<td style="text-align: left">SHIPMENT_ID</td>
<td style="text-align: left">INT(N)</td>
<td>FALSE</td>
</tr>
<tr>
<td style="text-align: left">FLAVOR</td>
<td style="text-align: left">VARCHAR(N)</td>
<td>FALSE</td>
</tr>
<tr>
<td style="text-align: left">TOTAL_ORDER</td>
<td style="text-align: left">INT(N)</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p>상반기에 판매된 아이스크림의 맛을 총주문량을 기준으로 내림차순 정렬하고 총주문량이 같다면 출하 번호를 기준으로 오름차순 정렬하여 조회하는 SQL 문을 작성해주세요.</p>

<hr>

<h5>예시</h5>

<p>예를 들어 <code>FIRST_HALF</code> 테이블이 다음과 같을 때 </p>
<table class="table">
        <thead><tr>
<th>SHIPMENT_ID</th>
<th>FLAVOR</th>
<th>TOTAL_ORDER</th>
</tr>
</thead>
        <tbody><tr>
<td>101</td>
<td>chocolate</td>
<td>3200</td>
</tr>
<tr>
<td>102</td>
<td>vanilla</td>
<td>2800</td>
</tr>
<tr>
<td>103</td>
<td>mint_chocolate</td>
<td>1700</td>
</tr>
<tr>
<td>104</td>
<td>caramel</td>
<td>2600</td>
</tr>
<tr>
<td>105</td>
<td>white_chocolate</td>
<td>3100</td>
</tr>
<tr>
<td>106</td>
<td>peach</td>
<td>2450</td>
</tr>
<tr>
<td>107</td>
<td>watermelon</td>
<td>2150</td>
</tr>
<tr>
<td>108</td>
<td>mango</td>
<td>2900</td>
</tr>
<tr>
<td>109</td>
<td>strawberry</td>
<td>3100</td>
</tr>
<tr>
<td>110</td>
<td>melon</td>
<td>3150</td>
</tr>
<tr>
<td>111</td>
<td>orange</td>
<td>2900</td>
</tr>
<tr>
<td>112</td>
<td>pineapple</td>
<td>2900</td>
</tr>
</tbody>
      </table>
<p>상반기 아이스크림 맛을 총주문량을 기준으로 내림차순 정렬하고 총주문량이 같은 경우 출하 번호를 기준으로 오름차순 정렬하면 chocolate, melon, white_chocolate, strawberry, mango, orange, pineapple, vanilla, caramel, peach, watermelon, mint_chocolate 순서대로 조회되어야 합니다. 따라서 SQL문을 실행하면 다음과 같이 나와야 합니다. </p>
<table class="table">
        <thead><tr>
<th>FLAVOR</th>
</tr>
</thead>
        <tbody><tr>
<td>chocolate</td>
</tr>
<tr>
<td>melon</td>
</tr>
<tr>
<td>white_chocolate</td>
</tr>
<tr>
<td>strawberry</td>
</tr>
<tr>
<td>mango</td>
</tr>
<tr>
<td>orange</td>
</tr>
<tr>
<td>pineapple</td>
</tr>
<tr>
<td>vanilla</td>
</tr>
<tr>
<td>caramel</td>
</tr>
<tr>
<td>peach</td>
</tr>
<tr>
<td>watermelon</td>
</tr>
<tr>
<td>mint_chocolate</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

문제는 현재 `FIRST_HALF` 테이블에서 총주문량이 큰값을 내림차순으로 정렬하고   
총주문량이 같다면 출하번호 기준으로 오름차순으로 정렬하는 문제이다.

```RoomSql
SELECT F.FLAVOR FROM FIRST_HALF F
ORDER BY TOTAL_ORDER DESC, SHIPMENT_ID ASC;
```

ORDER BY를 통해 내림,오름차순 정렬을 해주면 되는데   
여기서 정렬조건은 2개이다. 총 주문량(`TOTAL_ORDER`)을 기준으로 내림차순을 하고   
만약 총주문량이 같을 경우 출하번호 (`SHIPMENT_ID`)를 기준으로 오름차순 정렬을 해야한다.   

ORDER BY 문법에 첫번째 정렬값이 같다면 콤마(,)로 다음 조건을 적어주면   
해당 기준으로 정렬을 진행해준다.

 


<br/>

[인기있는 아이스크림]:https://school.programmers.co.kr/learn/courses/30/lessons/133024
