---
layout: post
title: "[Spring] JPA N+1 문제 분석 (+Querydsl)"
subtitle: 
categories: Spring
tags: [Spring, QueryDSL]
comments: true
published: true
---

소개에 앞서 [Github 레포지토리]에 코드가 올라가 있습니다.   
`Querydsl` 라이브러리를 사용하였고, `Spring Boot 2.7.10` 입니다.  


오늘은 Spring Data Jpa를 다루면서 발생한 N+1 문제에 대해 적어보려한다.  

<br/>

## JPA N+1 문제란?   

연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(N)만큼 연관관계의   
조회 쿼리가 추가로 발생하여 데이터를 읽어오는 현상을 얘기한다.   

JPA Repository로 실행하는 첫 쿼리에서 하위 엔티티까지 한번에 가져오지 않고   
하위 엔티티를 사용할 때 추가로 조회하기 때문에 발생한다.    

Fetch Type이 EAGER(즉시로딩), LAZY(지연로딩) 둘 다 발생하고  
쿼리가 발생하는 시점만 다를뿐이지 N+1문제는 여전하다.


<br/>

## 문제 발생 테스트   

### 테이블 설명

문제 발생 테스트를 위해 3개의 테이블을 만들었다.   
`POSTS(게시글)` - `POSTS_TAG(게시글 태그 관리)` - `TAG(태그)`   

`1:N:1` 관계를 가지며, POSTS쪽에서 TAG의 데이터를 가져오기 위해서   
LEFT JOIN으로 접근하는 과정에서 발생하는 N+1 문제를 체크해볼 생각이다.

![image](https://user-images.githubusercontent.com/95069395/230547251-d9b554a7-85a0-4430-9aa2-5b1f4548b431.png){: .align-left style="max-width: 100%"}

<br/>  

### 코드 준비   

🔖 **프로젝트 구조**

[Github 레포지토리]를 참고하시면 더 도움이 됩니다!

```text
├── main
│   ├── java
│   │   └── com
│   │       └── jpa
│   │           └── jpaoneplusn
│   │               ├── JpaoneplusnApplication.java
│   │               └── domain
│   │                   ├── config
│   │                   │   └── QuerydslConfig.java
│   │                   ├── entity
│   │                   │   ├── Posts.java
│   │                   │   ├── PostsTag.java
│   │                   │   └── Tag.java
│   │                   └── repository
│   │                       ├── PostsCustomRepository.java
│   │                       ├── PostsCustomRepositoryImpl.java
│   │                       ├── PostsRepository.java
│   │                       └── TagRepository.java
│   └── resources
│       ├── application.yml
│       ├── static
│       └── templates
└── test
    └── java
        └── com
            └── jpa
                └── jpaoneplusn
                    ├── JpaoneplusnApplicationTests.java
                    └── test
                        └── PostsTest.java

``` 

<br/>    

우선 모든 클래스의 코드를 올리기에는 이해하는데 복잡할 수 있기 때문에    
주요 코드인 `Entity`와 `PostsCustomRepositoryImpl`만 올리도록 하겠습니다.

**Posts.java**

```java
@Getter
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Posts {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    @OneToMany(mappedBy = "posts", cascade = CascadeType.PERSIST, fetch = FetchType.LAZY) // LAZY, EAGER 변경후 쿼리 시점변경
    private List<PostsTag> postsTags = new ArrayList<>();

    @Builder
    public Posts(Long id, String title) {
        this.id = id;
        this.title = title;
    }

    public void addPostsTag(PostsTag postsTag) {
        this.postsTags.add(postsTag);
    }
}
```

<br/>  

**PostsTag.java**

```java
@Getter
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class PostsTag {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "posts_id")
    private Posts posts;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "tag_id")
    private Tag tag;

    @Builder
    public PostsTag(Long id, Posts posts, Tag tag) {
        this.id = id;
        this.posts = posts;
        this.tag = tag;
    }
}
```

<br/>  

**Tag.java**

```java
@Getter
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Tag {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private String type;

    @OneToMany(mappedBy = "tag", cascade = CascadeType.PERSIST, fetch = FetchType.LAZY)
    private List<PostsTag> postsTags = new ArrayList<>();

    @Builder
    public Tag(Long id, String name, String type) {
        this.id = id;
        this.name = name;
        this.type = type;
    }

    public void addPostsTag(PostsTag postsTag) {
        this.postsTags.add(postsTag);
    }
}
```

<br/>  

**PostsCustomRepositoryImpl**

```java
@Repository
@RequiredArgsConstructor
public class PostsCustomRepositoryImpl implements PostsCustomRepository{

    private final JPAQueryFactory queryFactory;

    @Override
    public Posts findPost_noFetchJoin(Long id) {
        return queryFactory
                .selectFrom(posts)
                .leftJoin(posts.postsTags, postsTag)
                .leftJoin(postsTag.tag, tag)
                .where(posts.id.eq(id))
                .fetchOne();
    }

    @Override
    public Posts findPost_useFetchJoin(Long id) {
        return queryFactory
                .selectFrom(posts)
                .leftJoin(posts.postsTags, postsTag).fetchJoin()
                .leftJoin(postsTag.tag, tag).fetchJoin()
                .where(posts.id.eq(id))
                .fetchOne();
    }
}
```

<br/>  


### N+1 문제 테스트

우선 `application.yml`파일에 sql을 출력할 수 있도록 설정하였다.   
그리고 Test 코드를 통해 쿼리를 보면서 검증을 해보았다.

**PostsTest**

```java
@SpringBootTest
@Transactional
public class PostsTest {

    @Autowired
    EntityManager em;
    JPAQueryFactory queryFactory;
    @Autowired
    PostsRepository postsRepository;
    @Autowired
    TagRepository tagRepository;

    private static Long POST_ID;


    @BeforeEach
    public void setUp() {
        queryFactory = new JPAQueryFactory(em);
        Posts post = Posts.builder().title("계절별 강수량 조사 게시글 입니다.").build();
        Posts savedPost = postsRepository.save(post);
        POST_ID = savedPost.getId();

        // 1개의 게시글, 4개의 태그
        // 중간테이블 postTag = 1개의 게시글에 각각 태그를 연결해놓은 상황
        for (long i = 1; i <= 4; i++) {
            String[] seasons = new String[]{"봄", "여름", "가을", "겨울"};
            String season = seasons[(int) (i - 1L)];
            Tag tag = Tag.builder().name(season).type("계절").build();
            PostsTag postTag = PostsTag.builder().tag(tag).posts(post).build();
            tag.addPostsTag(postTag);
            tagRepository.save(tag);
        }
    }
}
```

가독성을 위해 코드를 분리해서 설명하겠다.    
첫번쨰로 @BeforeEach를 통해서 Posts, Tag, PostsTag에 정보를 넣어 주었다.   
총4개씩 넣어 주었고 Repository에 `.save()`를 이용해 테이블에 저장해주었다.    
여기서 PostsTag는 casecade를 이용해 저장되기 때문에 Repository를 따로 만들지 않았다.     

|      ||      |
|:----:|:----:|:----:|

<br/>  

본격적인 테스트는 아래에서 부터 설명하겠다.   

#### 1. N+1 문제 테스트   
첫번째로 영속성 컨텍스트를 초기화 해주어야한다.   
초기화 시켜주지 않는다면 이미 영속화되어 있기 때문에    
문제 발생상황이 제대로 체크가 되지 않을 수 있다.

```java
    @Test
    @DisplayName("N+1 문제 발생하는 테스트 케이스")
    public void jpaProblem() {
    
        // 영속성 컨텍스트 초기화
        em.flush();
        em.clear();

        System.out.println("============= 쿼리 시작 =============");
        Posts findPost = postsRepository.findPost_noFetchJoin(POST_ID);


        System.out.println("============= PostsTag 가져오기 =============");
        for (PostsTag postsTag : findPost.getPostsTags()) {
            System.out.println("PostsTag id = " + postsTag.getId());
        }

        // Tag정보를 가져올때 N+1문제가 발생하는 모습!!
        System.out.println("============= Tag 가져오기 =============");
        for (PostsTag postsTag : findPost.getPostsTags()) {
            System.out.println("Tag Name = " + postsTag.getTag().getName());
        }
    }
```

그리고 프린트출력문으로 3가지 파트로 나누었고    
For문을 Post의 PostTags, Tag를 순회하도록하였다.  
처음에 레포지토리에서 쿼리를 가져오고 다른 테이블에 접근하도록 나누었다.   

`postsRepository.findPost_noFetchJoin(Long id);`를 호출하여 테스트하였다.  
(위에 Querydsl 코드로 작성된 클래스를 참조)

현재 구분하기 쉽게 `Posts` 엔티티나 `PostsTag`엔티티나 FetchType을 LAZY로 설정했다.  
(EAGER로 하여도 쿼리 출력 시점만 다르고 N+1문제는 발생합니다.)

**LAZY 설정**
```roomsql
============= 쿼리 시작 =============
Hibernate: 
    select
        posts0_.id as id1_0_,
        posts0_.title as title2_0_ 
    from
        posts posts0_ 
    left outer join
        posts_tag poststags1_ 
            on posts0_.id=poststags1_.posts_id 
    left outer join
        tag tag2_ 
            on poststags1_.tag_id=tag2_.id 
    where
        posts0_.id=?
============= PostsTag 가져오기 =============
Hibernate: 
    select
        poststags0_.posts_id as posts_id2_1_0_,
        poststags0_.id as id1_1_0_,
        poststags0_.id as id1_1_1_,
        poststags0_.posts_id as posts_id2_1_1_,
        poststags0_.tag_id as tag_id3_1_1_ 
    from
        posts_tag poststags0_ 
    where
        poststags0_.posts_id=?
PostsTag id = 1
PostsTag id = 2
PostsTag id = 3
PostsTag id = 4
============= Tag 가져오기 =============
Hibernate: 
    select
        tag0_.id as id1_2_0_,
        tag0_.name as name2_2_0_,
        tag0_.type as type3_2_0_ 
    from
        tag tag0_ 
    where
        tag0_.id=?
Tag Name = 봄
Hibernate: 
    select
        tag0_.id as id1_2_0_,
        tag0_.name as name2_2_0_,
        tag0_.type as type3_2_0_ 
    from
        tag tag0_ 
    where
        tag0_.id=?
Tag Name = 여름
Hibernate: 
    select
        tag0_.id as id1_2_0_,
        tag0_.name as name2_2_0_,
        tag0_.type as type3_2_0_ 
    from
        tag tag0_ 
    where
        tag0_.id=?
Tag Name = 가을
Hibernate: 
    select
        tag0_.id as id1_2_0_,
        tag0_.name as name2_2_0_,
        tag0_.type as type3_2_0_ 
    from
        tag tag0_ 
    where
        tag0_.id=?
Tag Name = 겨울
```

실제 쿼리를 확인해보면 Hibernate로 구분을 하면 되는데      
Post와 PostTags Select하는 쿼리는 1개만 출력되었지만     
PostTags에서 Tag로 접근을할때 보면 4개의 쿼리가 발생한 것을 볼 수 있다.  
즉, 하위 엔티티까지 하번에 가져오지 않고 조회할때 추가로 조회 쿼리가 발생하고  
이것이 태그의 수량 4개만큼 발생하여 N+1문제가 발생한 것을 확인할 수 있다.

<br/>

만약 EAGER로 변경하게될 경우

**EAGER 설정**
```roomsql
============= 쿼리 시작 =============
Hibernate: 
    select
        posts0_.id as id1_0_,
        posts0_.title as title2_0_ 
    from
        posts posts0_ 
    left outer join
        posts_tag poststags1_ 
            on posts0_.id=poststags1_.posts_id 
    left outer join
        tag tag2_ 
            on poststags1_.tag_id=tag2_.id 
    where
        posts0_.id=?
Hibernate: 
    select
        poststags0_.posts_id as posts_id2_1_0_,
        poststags0_.id as id1_1_0_,
        poststags0_.id as id1_1_1_,
        poststags0_.posts_id as posts_id2_1_1_,
        poststags0_.tag_id as tag_id3_1_1_,
        tag1_.id as id1_2_2_,
        tag1_.name as name2_2_2_,
        tag1_.type as type3_2_2_ 
    from
        posts_tag poststags0_ 
    left outer join
        tag tag1_ 
            on poststags0_.tag_id=tag1_.id 
    where
        poststags0_.posts_id=?
============= PostsTag 가져오기 =============
PostsTag id = 1
PostsTag id = 2
PostsTag id = 3
PostsTag id = 4
============= Tag 가져오기 =============
Tag Name = 봄
Tag Name = 여름
Tag Name = 가을
Tag Name = 겨울
```
현재 위의 예제에서는 POST를 1개만 조회하기 때문에  
EAGER로 변경시 JOIN이 이루어져 1번만 더 쿼리가 발생한 것을 볼 수있다.   

예를들어 List로 여러개의 POST를 받게될 경우 N+1 문제가 발생할 것이다.  


<br/>  

#### 2. N+1 해결 테스트   
이렇게 콘솔을 통해서 N+1문제가 발생하고 있다는걸 확인했다.  
그러면 해결하려면 어떻게해야하는가?

해결방법으로는 `fetchJoin`을 사용하는 방법이 있다.   
`findPost_useFetchJoin(Long id)` 현재 프로젝트에서 메서드를 참고하면된다.
```java
    @Override
    public Posts findPost_useFetchJoin(Long id) {
        return queryFactory
            .selectFrom(posts)
            .leftJoin(posts.postsTags, postsTag).fetchJoin()
            .leftJoin(postsTag.tag, tag).fetchJoin()
            .where(posts.id.eq(id))
            .fetchOne();
    }
```

`findPost_useFetchJoin(Long id)` 코드에서 `fetchJoin()`을 붙여준게 끝이다.   

그럼 이메서드를 호출한 테스트 코드를 확인해보자

```java
    @Test
    @DisplayName("N+1 문제 해결한 테스트 케이스")
    public void jpaProblemSolve() {

        // 영속성 컨텍스트 초기화
        em.flush();
        em.clear();

        System.out.println("============= 쿼리 시작 =============");
        Posts findPost = postsRepository.findPost_useFetchJoin(POST_ID);

        System.out.println("============= PostsTag 가져오기 =============");
        for (PostsTag postsTag : findPost.getPostsTags()) {
            System.out.println("PostsTag id = " + postsTag.getId());
        }

        System.out.println("============= Tag 가져오기 =============");
        for (PostsTag postsTag : findPost.getPostsTags()) {
            System.out.println("Tag Name = " + postsTag.getTag().getName());
        }
    }
```
똑같은 테스트 코드에서 `postsRepository.findPost_useFetchJoin(Long id);`만 변경되었다.   

실제 SQL문을 확인해보면서 차이점을 느껴보자   
FetchType 설정은 LAZY, EAGER 똑같이 출력된다.   
 

```roomsql
============= 쿼리 시작 =============
Hibernate: 
    select
        posts0_.id as id1_0_0_,
        poststags1_.id as id1_1_1_,
        tag2_.id as id1_2_2_,
        posts0_.title as title2_0_0_,
        poststags1_.posts_id as posts_id2_1_1_,
        poststags1_.tag_id as tag_id3_1_1_,
        poststags1_.posts_id as posts_id2_1_0__,
        poststags1_.id as id1_1_0__,
        tag2_.name as name2_2_2_,
        tag2_.type as type3_2_2_ 
    from
        posts posts0_ 
    left outer join
        posts_tag poststags1_ 
            on posts0_.id=poststags1_.posts_id 
    left outer join
        tag tag2_ 
            on poststags1_.tag_id=tag2_.id 
    where
        posts0_.id=?
============= PostsTag 가져오기 =============
PostsTag id = 1
PostsTag id = 2
PostsTag id = 3
PostsTag id = 4
============= Tag 가져오기 =============
Tag Name = 봄
Tag Name = 여름
Tag Name = 가을
Tag Name = 겨울
```
객체를 가져오는 순간 쿼리가 1개만 출력된 모습을 확인할 수 있다.   
실제로 N+1 문제가 발생한 쿼리랑 차이를 느낄 수 있고   
해당 API를 만번 조회한다치면 쿼리의 양도 N배나 차이가 나게되는 것이다.   

<br/>   

---   

오늘은 이렇게 N+1 문제가 발생하는 이유와 해결방법을 알아보았는데   
`fetchJoin()`외에도 EntityGraph, Batch Size를 통해 해결이 가능하다고한다.   
해당 방법에 대해서는 추후 포스팅을 해보도록하려하고, fetchJoin에 대한 원리 공부도   
필요하다고 느껴진다..



<br/>
<br/>

[Github 레포지토리]:https://github.com/MyCatlikesChuru/JavaPersisitenceAPIstudy/tree/main/jpaoneplusn