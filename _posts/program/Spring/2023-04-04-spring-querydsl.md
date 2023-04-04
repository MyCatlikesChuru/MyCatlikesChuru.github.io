---
layout: post
title: "[Spring] QueryDSL 공부 및 사용하기"
subtitle: 
categories: Spring
tags: [Spring, QueryDSL]
comments: true
published: true
---

## QueryDSL   

### QueryDSL 너는 뭐니?🤔   

Query Domain Specific Language의 약자이다.    
JPQL을 Java 코드로 작성할 수 있도록 하는 라이브러리이다.   

JPQL과 다르게 사용함으로써 얻을 수 있는 이점은   
1. 문자가 아닌 코드로 쿼리를 작성함으로써, 컴파일 시점에 문법 오류를 확인할 수 있다. 
2. 자동 완성 등 IDE의 도움을 받을 수 있다.
3. 동적인 쿼리 작성이 편하다.

*동적쿼리?   
파라미터가 어떻게 오는지에 따라 조건이 변경되어지는 쿼리를 말한다.   


<br/>   

### 설정하기   

**gradle 설정**  

```java
buildscript { // Spring Boot 2.6 이상일 경우
	ext {
		queryDslVersion = "5.0.0"
	}
}

plugins {
	id 'org.springframework.boot' version '2.7.7'
	id 'io.spring.dependency-management' version '1.0.15.RELEASE'
	//querydsl 추가
	id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
	id 'java'
}

group = 'study'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'

	//querydsl 추가
	implementation "com.querydsl:querydsl-jpa:${queryDslVersion}"
	implementation "com.querydsl:querydsl-apt:${queryDslVersion}"

	// 쿼리를 이쁘게 출력해주는 라이브러리
	implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.5.8'

	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

tasks.named('test') {
	useJUnitPlatform()
}

//querydsl 추가 시작
def querydslDir = "$buildDir/generated/querydsl"

querydsl {
	jpa = true
	querydslSourcesDir = querydslDir
}
sourceSets {
	main.java.srcDir querydslDir
}
compileQuerydsl{
	options.annotationProcessorPath = configurations.querydsl
}
configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
	querydsl.extendsFrom compileClasspath
}
//querydsl 추가 끝
```

`Gradle > Tasks > other > compileQuerydsl`을 눌러 빌드하면   

`def querydslDir = "$buildDir/generated/querydsl"`에 의해서       
build > generated 패지키에 Q타입 클래스가 생긴다.

<br/>

**❗️ 주의점 ❗️**  
generated된 파일들은 git에 올리면 안된다.   
즉, gitignore처리를 해줘야한다. 보통 build파일을 gitignore하기 때문에   
build 패키지안에 generated 폴더를 넣어주면된다. (gradle에서 설정)

![image](https://user-images.githubusercontent.com/95069395/229707216-aece56b0-bc8c-4619-a4ec-24454e83486d.png){: .align-left style="max-width: 100%"}

<br/>   

### 사용하기

Member, Team을 이용한 예제를 작성해보려한다.   

**Member.java**
```java
@Entity
@Getter 
@Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "username", "age"}) // team은 넣을경우 무한루프돔
public class Member {

    @Id @GeneratedValue
    @Column(name = "member_id")
    private Long id;
    private String username;
    private int age;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "team_id")
    private Team team;

    public Member(String username){
        this(username, 0);
    }

    public Member(String username, int age){
        this(username, age, null);
    }

    public Member(String username, int age, Team team){
        this.username = username;
        this.age = age;
        if(team != null){
            changeTeam(team);
        }
    }

    public void changeTeam(Team team){
        this.team = team;
        team.getMembers().add(this);
    }

}
```

<br/>

**Team.java**   
```java
@Entity
@Getter 
@Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@ToString(of = {"id", "name"})
public class Team {

    @Id @GeneratedValue
    private Long id;
    private String name;

    @OneToMany(mappedBy = "team")
    private List<Member> members = new ArrayList<>();

    public Team(String name){
        this.name = name;
    }
}
```  

<br/>   

빌드하여 QTeam, QMember 클래스가 생성되었다면   
테스트코드를 통해 JPQL과 Querydsl을 비교해보자   

**1). JPQL로 작성한 테스트 코드**

```java
@SpringBootTest
@Transactional
public class QuerydslBasicTestPractice {

    @Autowired
    EntityManager em;

    @BeforeEach
    public void before() {
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

    @Test
    public void startJPQL() {
        Member findMember = em.createQuery("select m from Member m where m.username = :username", Member.class)
                .setParameter("username", "member1")
                .getSingleResult();

        assertThat(findMember.getUsername()).isEqualTo("member1");
    }
}
```

팀 객체를 2개 만들었다. (teamA, teamB)   
그리고 멤버를 4개 만들어 teamA, teamB에 속하도록 하였다.   

이중에서 Member의 username이 `member1`로 되어있는 테이블을 조회하는 JPQL 문법이다.   
파라미터 바인딩을 통해 `:username`의 `member1`을 넣어 주었다.

```roomsql
select
    member0_.member_id as member_i1_1_,
    member0_.age as age2_1_,
    member0_.team_id as team_id4_1_,
    member0_.username as username3_1_ 
from
    member member0_ 
where
    member0_.username=?
```
실제로 콘솔창에 출력되어진 쿼리를 보아도 where 절에 ?가 들어간 모습을 볼 수 있다.


<br/>

**2). Querydsl로 작성한 테스트 코드**

```java
@SpringBootTest
@Transactional
public class QuerydslBasicTestPractice {

    @Autowired
    EntityManager em;

    @BeforeEach
    public void before() {
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
}
```  
위와 동일한 요구사항으로 querydsl을 이용하여 작성한 코드이다.   
JPAQueryFactory 객체와 Q타입 Member객체를 만들어 작성하였다.   

그리고 where절에서 username.eq를 통해 `member1`과 같은 항목을 찾고 있다.   
여기서 JPQL과 다른점은 파라미터 바인딩을 하지않았지만, 자동으로 파라미터 바인딩을 해준다.   

```roomsql
select
        member0_.member_id as member_i1_1_,
        member0_.age as age2_1_,
        member0_.team_id as team_id4_1_,
        member0_.username as username3_1_ 
    from
        member member0_ 
    where
        member0_.username=?
```
위의 쿼리는 querydsl을 사용하였을 떄 출력되어진 쿼리문인데    
역시 JPQL과 동일하게 파라미터 바인딩이 되어진 모습이다.

<br/>   
<br/>   

여기까지가 기본적인 설정과 사용법에 대한 내용이고    
사용해본결과 정말 컴파일시 오류 확인이 정말 용이하고    
`.`을 입력해 사용가능한 문법들이 쭉 나오니깐 코드 작성할때 어시스턴스가   
정말 편하게 느껴졌다. 좀 더 사용해보면서 동적쿼리에 얼마나 편하게 작업이가능한지?   
여러가지 문법이 무엇이 있는지도 배워보도록 하자 !!