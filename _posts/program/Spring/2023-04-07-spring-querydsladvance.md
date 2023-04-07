---
layout: post
title: "[Spring] QueryDSL DTO 조회 방법"
subtitle: 실전! Querydsl 김영한님 강의 정리
categories: Spring
tags: [Spring, QueryDSL]
comments: true
published: true
---

## QueryDSL  

### 테스트 코드   

이전에 작성했던 [QueryDSL 기초설정 및 JPQL과 비교해보기]에서   
이어서 작업을 진행하였다. 클래스와 테스트코드는 동일하게 사용했고

```java
public class QuerydslBasicTestPractice {

   @Autowired
   EntityManager em;

   JPAQueryFactory queryFactory;

   @PersistenceUnit
   EntityManagerFactory emf;

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

### DTO 클래스 작성

```java
@Data
@NoArgsConstructor
public class MemberDto {

    private String username;
    private int age;

    public MemberDto(String username, int age) {
        this.username = username;
        this.age = age;
    }
}
```

DTO클래스는 위와 같이 작성했다.   

<br/>  

### 프로젝션과 결과 반환 - DTO 조회

#### JPQL을 이용한 방법   

먼저 QueryDsl 사용 이전에 어떻게 사용하였는지?  
순수 JPA에서 DTO를 조회할때 사용하는 방법이다.  

```java
    @Test
    public void findDtoByJPQL() {
        List<MemberDto> result = em.createQuery("select new study.querydsl.dto.MemberDto(m.username, m.age) from Member m", MemberDto.class).getResultList();

        for (MemberDto memberDto : result) {
            System.out.println("memberDto = " + memberDto);
        }
    }
```

new 명령어를 이용해 패키지 이름을 적어줘야해서 지저분해지는 코드고   
패지키이름을 매번적기에 상당히 번거롭다.  

<br/>  

#### QueryDsl을 이용한 방법   

결과를 DTO로 반환할 떄 사용하는 방법은 총 3가지가 있다.  

1. 프로퍼티 접근
2. 필드 직접 접근
3. 생성자 사용

예제를 통해 살펴보자   

<br/>

**1). 프로퍼티 접근방식**  

`getter`, `setter`를 이용한 값을 주입하는 방법이다.

```java    
    @Test
    public void findDtoBySetter() {
        List<MemberDto> result = queryFactory
                .select(Projections.bean(
                        MemberDto.class,
                        member.username,
                        member.age))
                .from(member)
                .fetch();

        for (MemberDto memberDto : result) {
            System.out.println("memberDto = " + memberDto);
        }
    }
    
// 출력 내용
memberDto = MemberDto(username=member1, age=10)
memberDto = MemberDto(username=member2, age=20)
memberDto = MemberDto(username=member3, age=30)
memberDto = MemberDto(username=member4, age=40)
```  

`Projections.bean()`을 이용해 Dto타입을 지정해주고  
저장할 데이터를 지정해주면 된다. 여기서 중요한점은   

`getter`, `setter` 가 있어야 가능하다.   
(MemberDto에는 `@Data`가 있어서 getter,setter를 사용함)  

또한 기본생성자가 존재해야 가능하다.

<br/>   

**2). 필드 직접 접근방식**   

위의 코드와 동일하지만 `Projections.fields()`만 변경 되었다.

```java
    @Test
    public void findDtoByField() {
        List<MemberDto> result = queryFactory
                .select(Projections.fields(
                        MemberDto.class,
                        member.username,
                        member.age))
                .from(member)
                .fetch();

        for (MemberDto memberDto : result) {
            System.out.println("memberDto = " + memberDto);
        }
    }
```
1번과의 차이점은 `getter`, `setter`가 없어도 가능하다는 점이다.   
필드에 바로 데이터를 삽입을 해준다. 여기서도 기본생성자는 있어야한다.   


<br/>  

**3). 생성자 사용방식**

생성자 사용방식도 위의 코드와 동일하지만 `Projections.constructor()`만 변경 되었다.

```java
    @Test
    public void findDtoByConstructor() {
        List<MemberDto> result = queryFactory
                .select(Projections.constructor(
                        MemberDto.class,
                        member.username,
                        member.age))
                .from(member)
                .fetch();

        for (MemberDto memberDto : result) {
            System.out.println("memberDto = " + memberDto);
        }
    }
```

생성자를 이용하는방법이 `MemberDto` 클래스의 생성자를 이용해서 값을 넣어준다.   
이와 같은 경우는 기본생성자가 없어도 가능하고   
```java
    public MemberDto(String username, int age) {
        System.out.println("나호출됨");
        this.username = username;
        this.age = age;
    }
```
와 같은 생성자가 존재하는데 위에 생성자가 직접 호출되기 때문에    
생성자 안에 출력을 넣어 `System.out.println("나호출됨");` 테스트를 실행하면   

`나호출됨`이라는 출력문이 4번 출력된 것을 확인할 수 있다.   
(member 테이블에 4개가 있기 떄문에)  

<br/>   

**❗️필드 접근방식에서 Q타입 객체와 Dto의 이름이 다를경우**  

예를 들어 `Member` 클래스의 `이름이 = username` 으로 지정되어 있고   
`UserDto`의 `이름이 = name` 으로 지정되어 있다고 가정해보자   

```java
@Data
public class UserDto {

    private String name;
    private int age;
}
```
`UserDto` 클래스가 이렇게 지정되어 있다고 가정하고   
2번 방법인 필드 직접 접근방식을 사용해서 작성해보면   

```java
    @Test
    public void findUserDtoByField() {
        List<UserDto> result = queryFactory
                .select(Projections.fields(
                        UserDto.class,
                        member.username,
                        member.age))
                .from(member)
                .fetch();

        for (UserDto userDto : result) {
            System.out.println("userDto = " + userDto);
        }
    } 
// 출력 내용  
userDto = UserDto(name=null, age=10)
userDto = UserDto(name=null, age=20)
userDto = UserDto(name=null, age=30)
userDto = UserDto(name=null, age=40)
```  
와 같은 방법으로 쿼리를 작성할 수 있다. 하지만 위와 같이 name은 null로   
표현되는 모습을 볼 수 있다. 이유는 같은 이름이 매칭이 안되기 때문이다.   

이를 해결하기 위해서 우리는 별칭(AS)를 붙여 해결이 가능하다.   

```java
    @Test
    public void findUserDtoByField() {
        List<UserDto> result = queryFactory
                .select(Projections.fields(
                        UserDto.class,
                        member.username.as("name"), // <- Alias 붙인곳
                        member.age))
                .from(member)
                .fetch();

        for (UserDto userDto : result) {
            System.out.println("userDto = " + userDto);
        }
    }
// 출력 내용 
userDto = UserDto(name=member1, age=10)
userDto = UserDto(name=member2, age=20)
userDto = UserDto(name=member3, age=30)
userDto = UserDto(name=member4, age=40)
```
와 같이 `.as()`를 이용해 Dto이름과 Entity의 이름이 상이해도 해결이 가능하다.
 
서브쿼리의 별칭을 붙여야할 경우에는   
```java
ExpressionUtils.as(JPAExpressions
        .select(memberSub.age.max())
        .from(memberSub), "age")
```
`ExpressionUtils.as(서브쿼리, 별칭)`과 같은 방식으로 사용할 수 있다.   




<br/>

[QueryDSL 기초설정 및 JPQL과 비교해보기]: https://mycatlikeschuru.github.io/spring/2023/04/04/spring-querydsl.html