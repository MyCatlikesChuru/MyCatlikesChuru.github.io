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

    @Builder
    public Tag(Long id, String name, String type) {
        this.id = id;
        this.name = name;
        this.type = type;
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
    public List<Posts> findAll_noFetchJoin() {
        return queryFactory
                .selectFrom(posts)
                .leftJoin(posts.postsTags, postsTag)
                .leftJoin(postsTag.tag, tag)
                .where(posts.id.eq(postsTag.id))
                .fetch();
    }

    @Override
    public List<Posts> findAll_useFetchJoin() {
        return queryFactory
                .selectFrom(posts)
                .leftJoin(posts.postsTags, postsTag).fetchJoin()
                .leftJoin(postsTag.tag, tag).fetchJoin()
                .where(posts.id.eq(postsTag.id))
                .fetch();
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


    @BeforeEach
    public void setUp() {
        queryFactory = new JPAQueryFactory(em);

        for (long i = 1; i <= 4; i++) {
            Posts post = Posts.builder().title("제목 "+i).build();
            Tag tag = Tag.builder().name("태그 "+i).type("타입 "+i).build();
            PostsTag postTag = PostsTag.builder().tag(tag).posts(post).build();
            post.addPostsTag(postTag);
            tagRepository.save(tag);
            postsRepository.save(post);
//            em.persist(tag);
//            em.persist(post);
        }
    }
}
```

가독성을 위해 코드를 분리해서 설명하겠다.    
첫번쨰로 @BeforeEach를 통해서 Posts, Tag, PostsTag에 정보를 넣어 주었다.   
총4개씩 넣어 주었고 Repository에 `.save()`를 이용해 테이블에 저장해주었다.    
여기서 PostsTag는 casecade를 이용해 저장되기 때문에 Repository를 따로 만들지 않았다.   

<br/>  

본격적인 테스트는 아래에서 부터 설명하겠다.   

#### 1. N+1 문제 테스트   
첫번째로 영속성 컨텍스트를 초기화 해주어야한다.   
초기화 시켜주지 않는다면 이미 영속화되어 있기 때문에    
문제 발생상황이 체크가 되지 않는다.

```java
    @Test
    @DisplayName("N+1 문제 발생하는 테스트 케이스")
    public void jpaProblem() {

        // 영속성 컨텍스트 초기화
        em.flush();
        em.clear();

        System.out.println("============= 쿼리 시작 =============");
        List<Posts> findPosts = postsRepository.findAll_noFetchJoin();

        System.out.println("============= PostsTag 가져오기 =============");
        List<PostsTag> findPostsTag = findPosts.get(0).getPostsTags();
        System.out.println("PostsTag id = " + findPostsTag.get(0).getId());

        System.out.println("============= Tag 가져오기 =============");
        Tag tag = findPostsTag.get(0).getTag();
        System.out.println("Tag Name = " + tag.getName());
    }
```

그리고 프린트출력문으로 3가지 파트로 나누었고    
처음에 레포지토리에서 쿼리를 가져오고 다른 테이블에 접근하도록 나누었다.   

`postsRepository.findAll_noFetchJoin();`를 호출하여 테스트하였다.  
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
        posts0_.id=poststags1_.id
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
Tag Name = 태그 1
```

실제 쿼리를 확인해보면 Hibernate로 구분을 하면 되는데 3번의 쿼리가 나오게되었다.  
즉 연관관계가 설정된 엔티티를 조회하는데 쿼리가 추가로 발생하였기 때문에   
N+1문제가 발생하였다는 것이다.

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
        posts0_.id=poststags1_.id
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
============= Tag 가져오기 =============
Tag Name = 태그 1
```

쿼리가 5번 나오게되었다. 즉시로딩이기 때문에 쿼리시작하는 부분에서   
모든 쿼리가 나오게 된 차이점만 있을뿐 N+1문제는 여전히 존재한다.   
오히려 쿼리가 2개나 더나오게되었다.   

<br/>  

#### 2. N+1 해결 테스트   
이렇게 콘솔을 통해서 N+1문제가 발생하고 있다는걸 확인했다.  
그러면 해결하려면 어떻게해야하는가?

해결방법으로는 `fetchJoin`을 사용하는 방법이 있다.   
`findAll_useFetchJoin()` 메서드를 참조하면된다.
```java
    @Override
    public List<Posts> findAll_useFetchJoin() {
        return queryFactory
                .selectFrom(posts)
                .leftJoin(posts.postsTags, postsTag).fetchJoin() // <- fetchJoin
                .leftJoin(postsTag.tag, tag).fetchJoin() // <- fetchJoin
                .where(posts.id.eq(postsTag.id))
                .fetch();
    }
```

`findAll_noFetchJoin()` 코드에서 `fetchJoin()`을 붙여준게 끝이다.   

그럼 이메서드를 호출한 테스트 코드를 확인해보자

```java
    @Test
    @DisplayName("N+1 문제 해결한 테스트 케이스")
    public void jpaProblemSolve() {

        // 영속성 컨텍스트 초기화
        em.flush();
        em.clear();

        System.out.println("============= 쿼리 시작 =============");
        List<Posts> findPosts = postsRepository.findAll_useFetchJoin();

        System.out.println("============= PostsTag 가져오기 =============");
        List<PostsTag> findPostsTag = findPosts.get(0).getPostsTags();
        System.out.println("PostsTag id = " + findPostsTag.get(0).getId());

        System.out.println("============= Tag 가져오기 =============");
        Tag tag = findPostsTag.get(0).getTag();
        System.out.println("Tag Name = " + tag.getName());
    }
```
똑같은 테스트 코드에서 `postsRepository.findAll_useFetchJoin();`만 변경되었다.   

실제 SQL문을 확인해보면서 차이점을 느껴보자   
FetchType 설정은 LAZY, EAGER 똑같이 출력된다.
 

```java
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
        posts0_.id=poststags1_.id
============= PostsTag 가져오기 =============
PostsTag id = 5
============= Tag 가져오기 =============
Tag Name = 태그 1
```

쿼리가 1개만 출력된 모습을 확인할 수 있다.   
실제로 N+1 문제가 발생한 쿼리랑 차이를 느낄 수 있고   
해당 API를 만번 조회한다치면 쿼리의 양도 3배나 차이가 나게되는 것이다.   

<br/>   

---   

오늘은 이렇게 N+1 문제가 발생하는 이유와 해결방법을 알아보았는데   
`fetchJoin()`외에도 EntityGraph, Batch Size를 통해 해결이 가능하다고한다.   
해당 방법에 대해서는 추후 포스팅을 해보도록하려하고, fetchJoin에 대한 원리 공부도   
필요하다고 느껴진다..



<br/>
<br/>

[Github 레포지토리]:https://github.com/MyCatlikesChuru/JavaPersisitenceAPIstudy/tree/main/jpaoneplusn