---
layout: post
title: "[Spring] QueryDSL 기본문법 사용하기"
subtitle: 실전! Querydsl 김영한님 강의 정리
categories: Spring
tags: [Spring, QueryDSL]
comments: true
published: true
---

## QueryDSL   

### 테스트 코드   

이전에 작성했던 [QueryDSL 기초설정 및 JPQL과 비교해보기]에서   
이어서 작업을 진행하였다. 동일하게 테스트코드는

```java
public class QuerydslBasicTestPractice {

   @Autowired
   EntityManager em;

   JPAQueryFactory queryFactory;

   @BeforeEach
   public void before() {
      queryFactory = new JPAQueryFactory(em);
      Team teamA = new Team("teamA");
      Team teamB = new Team("teamB");
      em.persist(teamA);
      em.persist(teamB);

      Member member1 = new Member("member1", 10, teamA);
      Member member2 = new Member("member2", 20, teamA);
      Member member3 = new Member("member3", 30, teamB);
      Member member4 = new Member("member4", 40, teamB);
      em.persist(member1);
      em.persist(member2);
      em.persist(member3);
      em.persist(member4);
   }
}
```

Team 객체 2개와 Member 객체 4개를 만들어서 테스트를 진행했다.   
아래의 코드는 위의 @BeforeEach를 통해 만들어진 객체를 기준으로 생각하면된다.  

<br/>

### Q타입 활용   

Q타입 객체를 만들어 사용이 가능하다.  
m이라는 이름으로 객체를 생성하여 queryFactorty를 이용한 사용법.

```java
    @Test
    public void queryDsl() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);
        QMember m = new QMember("m");

        Member findMember = queryFactory
                .select(m)
                .from(m)
                .where(m.username.eq("member1"))
                .fetchOne();

        assertThat(findMember.getUsername()).isEqualTo("member1");
    }
```

<br/>

위와 같이 사용도 가능하지만 QMember.member를 이용해 QMember 클래스에   
선언되어 있는 것을 이용하는 방법을 주로 사용한다. 여기서 QMember를 static import하여 사용한다.

```java
    @Test
    public void queryDsl() {
        JPAQueryFactory queryFactory = new JPAQueryFactory(em);

        Member findMember = queryFactory
                .select(QMember.member)
                .from(QMember.member)
                .where(QMember.member.username.eq("member1"))
                .fetchOne();

        assertThat(findMember.getUsername()).isEqualTo("member1");
    }
```

<br/>   

같은 테이블을 JOIN하는 경우에 첫번째와 같이 alias를 변경하여 사용하여 해결이 가능하다.   
하지만 그럴 경우가 아니면 일반적으로 static import하여 2번과 같이 사용한다.  

<br/>

### 검색 조건 쿼리   

`select`, `from`을   
`selectFrom`으로 합칠 수 있다.    

여러가지 검색 조건의 쿼리

```java
member.username.eq("member1") // username = 'member1'
member.username.ne("member1") // username != 'member1'
member.username.eq("member1").not // username != 'member1'

member.username.isNotNull() // 이름이 is not null

member.age.in(10,20) // age가 10이거나 20살인 사람
member.age.notIn(10,20) // age가 10이거나 20살이 아닌사람
member.age.between(10,30) // age가 10~30사이인 사람

member.age.goe(30) // age >= 30
member.age.gt(30) // age > 30
member.age.loe(30) // age <= 30
member.age.lt(30) // age < 30

member.username.like("member%") // like 검색
member.username.contains("member") // like `%member%` 검색
member.username.startsWith("member") // like `member%` 검색
```

기본적으로 JPQL에서 지원하는 모든 검색 조건을 제공한다.   
이를 이용해서 한번 작성해보자   

```java
    @Test
    public void search() {
        Member findMember = queryFactory
                .select(member)
                .from(member)
                .where(member.username.eq("member1")
                        .and(member.age.eq(10)))
                .fetchOne();
        
        assertThat(findMember.getUsername()).isEqualTo("member1");
        assertThat(findMember.getAge()).isEqualTo(10);
    }
```
member의 테이블을 조회하여   
username = 'member1' AND age = '10'인 행을 조회하는 쿼리이다.   

현재 usename,age가 조건에 부합하는 행은 하나가 존재하므로 해당 행의 대한 데이터가   
findMember를 통해 접근이 가능하다.   

여기서 `.and()`를 이용해 쿼리를 작성했지만 아래와 같이
```java
    @Test
    public void search() {
        Member findMember = queryFactory
                .select(member)
                .from(member)
                .where(member.username.eq("member1"),
                        member.age.eq(10))
                .fetchOne();

        assertThat(findMember.getUsername()).isEqualTo("member1");
        assertThat(findMember.getAge()).isEqualTo(10);
    }
```
`,`를 입력하여 체인을 걸어주면 기본적으로 `.and()`로 인식하게 되어 사용할 수 있다.   

<br/>

### 결과 조회 (fetch)   

1. fetch() : 리스트 조회, 데이터 없으면 빈 리스트 반환
2. fetchOne() : 단 건 조회
   - 결과가 없으면 : null
   - 결과가 둘 이상이면 : NonUniqueResultException 발생
3. fetchFirst() : limit(1)을 걸면서 fetchOne()하는 것과 동일 (=limit(1).fetchOne)
4. fetchResults() : 페이징 정보 포함, total count 쿼리 추가 실행
5. fetchCount() : count 쿼리로 변경해서 count 수 조회

결과를 조회하는 문법은 위와 같다.   
코드를 통해서 확인해보자   

```java
    @Test
    public void resultFetch() {
        // 1. fetch : 리스트 조회
        List<Member> fetch = queryFactory
                .selectFrom(member)
                .fetch();

        // 2. fetchOne : 단 건 조회
        Member fetchOne = queryFactory
                .selectFrom(member)
                .fetchOne();

        // 3. fetchFirst : limit(1).fetchOne()
        Member fetchFirst = queryFactory
                .selectFrom(member)
                .fetchFirst();

        // 4. fetchResults : 페이징 정보 포함
        QueryResults<Member> fetchResults = queryFactory
                .selectFrom(member)
                .fetchResults();

        fetchResults.getTotal();
        fetchResults.getLimit();
        fetchResults.getOffset();
        List<Member> content = fetchResults.getResults();
    }
```

1~3번까지는 많이 사용해보던 방식들이라 이해하기 쉬울 것이다.   
여기서 페이징 정보를 받는 `fetchResults`를 확인해보자   

해당 객체로 `.getTotal()`, `.getLimit()`, `.getOffset()`과 같은 페이징   
정보가 확인이 가능하고 `.getResults()`를 통해서 List로 정보만 확인이 가능하다   

페이징 정보 깨알상식으로 아래 적어본다.  
- total : 현재 조회된 모든값의 총 갯수  
- limit : 조회할 결과의 개수를 의미  
- offset : 조회를 시작할 기준점을 의미    

`fetchResults`를 실행하게 될 경우 쿼리가 2번 발생한다.  

```roomsql
select
      count(member0_.member_id) as col_0_0_ 
  from
      member member0_
```
```roomsql
select
      member0_.member_id as member_i1_1_,
      member0_.age as age2_1_,
      member0_.team_id as team_id4_1_,
      member0_.username as username3_1_ 
  from
      member member0_
```

`total`을 가져오기위한 쿼리 한개와   
`content`를 가져오는 쿼리 한개해서 총2개의 쿼리가 발생한다.

<br/>  

마지막으로 `fetchCount`를 살펴보자

```java
    @Test
    public void resultFetch() {
        // 5. fetchCount : count 수 조회
        long total = queryFactory
        .selectFrom(member)
        .fetchCount();
    }
```

`fetchResults`에서 쿼리가 2번 발생했었는데  
`fetchCount`를 하게되면 `total`값만 한번의 쿼리로 가져오게 된다.   

```roomsql
select
      count(member0_.member_id) as col_0_0_ 
  from
      member member0_
```

<br/>

### 정렬  

이번에는 정렬을 이용해보자   
`orderBy()` 메서드를 통해서 정렬을 사용할 수 있다.   
오름차순 = `asc()` / 내림차순 = `desc()`

정렬 조건을 정해보자   
멤버 3명을 추가할 것이다.    
usename이 null인 멤버 하나와, member5~6으로된 멤버 둘   
나이가 100살로 설정하고 해당 나이에서 정렬을 해보자   

1. 나이순으로 내림차순 
2. 이름순으로 오름차순
3. 이름이 null인 경우를 마지막으로 정렬

```java
    @Test
    public void sort() {
        em.persist(new Member(null, 100));
        em.persist(new Member("member5", 100));
        em.persist(new Member("member6", 100));

        List<Member> result = queryFactory
                .selectFrom(member)
                .where(member.age.eq(100))
                .orderBy(member.age.desc(), member.username.asc().nullsLast())
                .fetch();

        Member member5 = result.get(0);
        Member member6 = result.get(1);
        Member memberNull = result.get(2);

        assertThat(member5.getUsername()).isEqualTo("member5");
        assertThat(member6.getUsername()).isEqualTo("member6");
        assertThat(memberNull.getUsername()).isNull();
    }
```

`fetch()`로 반환하여 List형태로 3개의 Member 객체를 받을 수 있다.   
정렬순서대로 get(0)~get(2)하여 검증을 해보면    
이름순으로 정렬되고 마지막이 null로 찍힌 모습을 확인할 수 있다.   

`nullsLast()`를 하지않게된다면 첫번째가 null인 객체를 받게되어진다.   

<br/>   

### 페이징   

첫번쨰로 `offset()`,`limit()`를 이용해 원하는 항목만큼 가져올 수 있다.

```java
    @Test
    public void paging1() {
        List<Member> result = queryFactory
                .selectFrom(member)
                .orderBy(member.username.desc())
                .offset(1)
                .limit(2)
                .fetch();
        assertThat(result.size()).isEqualTo(2);
    }
```
member1~4까지 객체가 등록되어있고    
username으로 내림차순으로 설정해두었다.   
offset이 1이기 때문에 시작지점은 첫번째부터 시작하고 limit가 2이기 때문에   
2개의 데이터만 뽑아서 `fetch()`를 하게되면 List형태로 추출하게된다.   

Member(id=5, username=member3, age=30)   
Member(id=4, username=member2, age=20)   

객체를 출력하게되면 이렇게 나오게되어진다.

<br/>   

두번쨰로는 `fetchResults()`를 결과로 뽑아내는 방법이다.

```java
    @Test
    public void paging2() {
        QueryResults<Member> queryResults = queryFactory
                .selectFrom(member)
                .orderBy(member.username.desc())
                .offset(1)
                .limit(2)
                .fetchResults();

        assertThat(queryResults.getTotal()).isEqualTo(4);
        assertThat(queryResults.getLimit()).isEqualTo(2);
        assertThat(queryResults.getOffset()).isEqualTo(1);
        assertThat(queryResults.getResults()).isEqualTo(2);
    }
```

위와 동일한 방식이지만 결과를 `fetchResults()` 통해서 객체를 받았다.    
`.getTotal()`을 이용하면 총 4개의 객체가 나오기 떄문에 = `4`   
`.getLimit()`를 이용하면 2개로 설정하였기에 = `2`   
(여기서 limit를 설정하지 않았다면, Long타입의 최대값 9223372036854775807L이 표시된다.)   
`.getOffset()`를 이용하면 1로 설정하였기 때문에 = `1`   
`.getResults()`를 이용하면 최종적으로 반환된 행의 내용들이 표시되기 때문에 = `2` 가 되어진다.   


실제 출력쿼리를 확인해보면 아래와 같이 확인할 수 있다.

```roomsql
select
      member0_.member_id as member_i1_1_,
      member0_.age as age2_1_,
      member0_.team_id as team_id4_1_,
      member0_.username as username3_1_ 
  from
      member member0_ 
  order by
      member0_.username desc limit ? offset ?
```
limit, offset 파라미터 바인딩된 모습을 볼 수 있다.   

<br/>  

### 집합 

[//]: # ()
[//]: # ()
[//]: # (첫번째로는 select)

[//]: # ()
[//]: # (```java)

[//]: # (    @Test)

[//]: # (    public void aggregation&#40;&#41; {)

[//]: # (        List<Tuple> result = queryFactory)

[//]: # (                .select&#40;)

[//]: # (                        member.count&#40;&#41;,)

[//]: # (                        member.age.sum&#40;&#41;,)

[//]: # (                        member.age.avg&#40;&#41;,)

[//]: # (                        member.age.max&#40;&#41;,)

[//]: # (                        member.age.min&#40;&#41;)

[//]: # (                &#41;)

[//]: # (                .from&#40;member&#41;)

[//]: # (                .fetch&#40;&#41;;)

[//]: # ()
[//]: # (        Tuple tuple = result.get&#40;0&#41;;)

[//]: # (        assertThat&#40;tuple.get&#40;member.count&#40;&#41;&#41;&#41;.isEqualTo&#40;4&#41;;)

[//]: # (        assertThat&#40;tuple.get&#40;member.age.sum&#40;&#41;&#41;&#41;.isEqualTo&#40;100&#41;;)

[//]: # (        assertThat&#40;tuple.get&#40;member.age.avg&#40;&#41;&#41;&#41;.isEqualTo&#40;25&#41;;)

[//]: # (        assertThat&#40;tuple.get&#40;member.age.max&#40;&#41;&#41;&#41;.isEqualTo&#40;40&#41;;)

[//]: # (        assertThat&#40;tuple.get&#40;member.age.min&#40;&#41;&#41;&#41;.isEqualTo&#40;10&#41;;)

[//]: # (    })

[//]: # (```)




<br/>

[QueryDSL 기초설정 및 JPQL과 비교해보기]: https://mycatlikeschuru.github.io/spring/2023/04/04/spring-querydsl.html