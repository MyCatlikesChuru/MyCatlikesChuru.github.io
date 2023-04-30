---
layout: post
title: "[Programmers] 조건에 맞는 사용자와 총 거래금액 조회하기 (MySQL)"
subtitle: Level 3
categories: SQL
tags: [SQL, Programmers]
comments: true
published: true
---

## 📌 문제 : [조건에 맞는 사용자와 총 거래금액 조회하기]

### 📖 문제 설명  

<p>다음은 중고 거래 게시판 정보를 담은 <code>USED_GOODS_BOARD</code> 테이블과 중고 거래 게시판 첨부파일 정보를 담은 <code>USED_GOODS_FILE</code> 테이블입니다. <code>USED_GOODS_BOARD</code> 테이블은 다음과 같으며 <code>BOARD_ID</code>, <code>WRITER_ID</code>, <code>TITLE</code>, <code>CONTENTS</code>, <code>PRICE</code>, <code>CREATED_DATE</code>, <code>STATUS</code>, <code>VIEWS</code>는 게시글 ID, 작성자 ID, 게시글 제목, 게시글 내용, 가격, 작성일, 거래상태, 조회수를 의미합니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>BOARD_ID</td>
<td>VARCHAR(5)</td>
<td>FALSE</td>
</tr>
<tr>
<td>WRITER_ID</td>
<td>VARCHAR(50)</td>
<td>FALSE</td>
</tr>
<tr>
<td>TITLE</td>
<td>VARCHAR(100)</td>
<td>FALSE</td>
</tr>
<tr>
<td>CONTENTS</td>
<td>VARCHAR(1000)</td>
<td>FALSE</td>
</tr>
<tr>
<td>PRICE</td>
<td>NUMBER</td>
<td>FALSE</td>
</tr>
<tr>
<td>CREATED_DATE</td>
<td>DATE</td>
<td>FALSE</td>
</tr>
<tr>
<td>STATUS</td>
<td>VARCHAR(10)</td>
<td>FALSE</td>
</tr>
<tr>
<td>VIEWS</td>
<td>NUMBER</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<p><code>USED_GOODS_USER</code> 테이블은 다음과 같으며 <code>USER_ID</code>, <code>NICKNAME</code>, <code>CITY</code>, <code>STREET_ADDRESS1</code>, <code>STREET_ADDRESS2</code>, <code>TLNO</code>는 각각 회원 ID, 닉네임, 시, 도로명 주소, 상세 주소, 전화번호를 를 의미합니다.</p>
<table class="table">
        <thead><tr>
<th>Column name</th>
<th>Type</th>
<th>Nullable</th>
</tr>
</thead>
        <tbody><tr>
<td>USER_ID</td>
<td>VARCHAR(50)</td>
<td>FALSE</td>
</tr>
<tr>
<td>NICKANME</td>
<td>VARCHAR(100)</td>
<td>FALSE</td>
</tr>
<tr>
<td>CITY</td>
<td>VARCHAR(100)</td>
<td>FALSE</td>
</tr>
<tr>
<td>STREET_ADDRESS1</td>
<td>VARCHAR(100)</td>
<td>FALSE</td>
</tr>
<tr>
<td>STREET_ADDRESS2</td>
<td>VARCHAR(100)</td>
<td>TRUE</td>
</tr>
<tr>
<td>TLNO</td>
<td>VARCHAR(20)</td>
<td>FALSE</td>
</tr>
</tbody>
      </table>
<hr>

<h5>문제</h5>

<p><code>USED_GOODS_BOARD</code>와 <code>USED_GOODS_USER</code> 테이블에서 완료된 중고 거래의 총금액이 70만 원 이상인 사람의 회원 ID, 닉네임, 총거래금액을 조회하는 SQL문을 작성해주세요. 결과는 총거래금액을 기준으로 오름차순 정렬해주세요.</p>

<hr>

<h5>예시</h5>

<p><code>USED_GOODS_BOARD</code> 테이블이 다음과 같고</p>
<table class="table">
        <thead><tr>
<th>BOARD_ID</th>
<th>WRITER_ID</th>
<th>TITLE</th>
<th>CONTENTS</th>
<th>PRICE</th>
<th>CREATED_DATE</th>
<th>STATUS</th>
<th>VIEWS</th>
</tr>
</thead>
        <tbody><tr>
<td>B0001</td>
<td>zkzkdh1</td>
<td>캠핑의자</td>
<td>가벼워요 깨끗한 상태입니다. 2개</td>
<td>25000</td>
<td>2022-11-29</td>
<td>SALE</td>
<td>34</td>
</tr>
<tr>
<td>B0002</td>
<td>miyeon89</td>
<td>벽걸이 에어컨</td>
<td>엘지 휘센 7평</td>
<td>100000</td>
<td>2022-11-29</td>
<td>SALE</td>
<td>55</td>
</tr>
<tr>
<td>B0003</td>
<td>dhfkzmf09</td>
<td>에어팟 맥스</td>
<td>에어팟 맥스 스카이 블루 색상 판매합니다.</td>
<td>450000</td>
<td>2022-11-26</td>
<td>DONE</td>
<td>67</td>
</tr>
<tr>
<td>B0004</td>
<td>sangjune1</td>
<td>파파야나인 포르쉐 푸쉬카</td>
<td>예민하신분은 피해주세요</td>
<td>30000</td>
<td>2022-11-30</td>
<td>DONE</td>
<td>78</td>
</tr>
<tr>
<td>B0005</td>
<td>zkzkdh1</td>
<td>애플워치7</td>
<td>애플워치7 실버 스텐 45미리 판매합니다.</td>
<td>700000</td>
<td>2022-11-30</td>
<td>DONE</td>
<td>99</td>
</tr>
</tbody>
      </table>
<p><code>USED_GOODS_USER</code> 테이블이 다음과 같을 때</p>
<table class="table">
        <thead><tr>
<th>USER_ID</th>
<th>NICKNAME</th>
<th>CITY</th>
<th>STREET_ADDRESS1</th>
<th>STREET_ADDRESS2</th>
<th>TLNO</th>
</tr>
</thead>
        <tbody><tr>
<td>cjfwls91</td>
<td>점심만금식</td>
<td>성남시</td>
<td>분당구 내정로 185</td>
<td>501호</td>
<td>01036344964</td>
</tr>
<tr>
<td>zkzkdh1</td>
<td>후후후</td>
<td>성남시</td>
<td>분당구 내정로 35</td>
<td>가동 1202호</td>
<td>01032777543</td>
</tr>
<tr>
<td>spdlqj12</td>
<td>크크큭</td>
<td>성남시</td>
<td>분당구 수내로 206</td>
<td>2019동 801호</td>
<td>01087234922</td>
</tr>
<tr>
<td>xlqpfh2</td>
<td>잉여킹</td>
<td>성남시</td>
<td>분당구 수내로 1</td>
<td>001-004</td>
<td>01064534911</td>
</tr>
<tr>
<td>dhfkzmf09</td>
<td>찐찐</td>
<td>성남시</td>
<td>분당구 수내로 13</td>
<td>A동 1107호</td>
<td>01053422914</td>
</tr>
</tbody>
      </table>
<p>SQL을 실행하면 다음과 같이 출력되어야 합니다.</p>
<table class="table">
        <thead><tr>
<th>USER_ID</th>
<th>NICKNAME</th>
<th>TOTAL_SALES</th>
</tr>
</thead>
        <tbody><tr>
<td>zkzkdh1</td>
<td>후후후</td>
<td>700000</td>
</tr>
</tbody>
      </table>

> 출처: 프로그래머스 코딩 테스트 연습, https://programmers.co.kr/learn/challenges

---

## 🗝 문제 풀이

1. 가격의 총합을 구하기 위한 SUM 함수를 사용
2. BOARD 기준으로 USER 테이블을 LEFT JOIN
3. 상태중 완료 상태인 'DONE'를 필터링하기위한 WHERE절 사용
4. 총 가격을 GROUP BY하기 위해 WRITER_ID를 기준으로 집계
5. 총 가격을 기준으로 오름차순 정렬

```roomsql
SELECT b.WRITER_ID,
    u.NICKNAME,
    SUM(b.PRICE) AS TOTAL_SALES
FROM USED_GOODS_BOARD b
LEFT JOIN USED_GOODS_USER u
ON b.WRITER_ID = u.USER_ID
WHERE b.STATUS = 'DONE'
GROUP BY b.WRITER_ID
HAVING SUM(b.PRICE) >= 700000
ORDER BY TOTAL_SALES ASC;
```



<br/>

[조건에 맞는 사용자와 총 거래금액 조회하기]:https://school.programmers.co.kr/learn/courses/30/lessons/164668
