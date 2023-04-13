---
layout: post
title: "[Spring] GraphQL 이란? 실습해보기"
subtitle: 
categories: Spring
tags: [Spring, QueryDSL]
comments: true
published: true
---

오늘은 REST API와 조금 다룬 GraphQL에 대해서    
기본적인 개념와 실습을 위해 프로젝트를 하나파서 만들어봤다.

내가 만든 [GitHub 레포지토리]를 참고하면 가장 기초적인 셋팅과   
사용법을 볼 수 있으니 참고바란다.

<br/>

## GraphQL

### GraphQL 이란?  

GraphQL은 REST API의 대안으로 페이스북에서 제시한 새로운 Web API 컨셉이라고한다.     

**<span style="color:#F04965">REST API</span>** 는 우리가 알듯이 다양한 HTTP Method가 서버에 존재하는 리소스와 대응되어서 동작한다.    
클라이언트의 요청사항과 리소스가 잘 들어맞지 않는다면 성능 문제가 발생할 수 있다.   

**<span style="color:#F04965">GraphQL</span>** 은 클라이언트 단일 요청에서 여러 하위 자원 탐색을 포함하여 원하는   
데이터만 정확하게 지정할 수 있다.   

<br/>   

이로서 얻을 수 있는 장점은 아래와 같다.

- **Overfetching**  
  기존의 Rest Api에선 api 호출 시 필요 이상의 정보를 전달받는 문제가 있었다.  
  GraphQL은 client에서 필요한 데이터만을 요청하므로 overfetching이 해결 된다.  

- **Endpoint**  
  Rest Api는 각 api마다 다른 endpoint가 있어서 이름을 짓거나 관련성이 있는 것들끼리 묶는 등의 관리가 어렵다.  
  GraphQL은 단 하나의 endpoint가 있어서 요청 주소가 매우 간단해진다.

<br/>  

### GraphQL 적용해보기   

#### 프로젝트 개요

이제 대략적인 개념을 이해했으니 서버에서 구현하여 테스트해보자   

Java 진영에서는 Spring 프레임워크를 이용하면 손쉽게 구현이 가능하다.   
기존에 MVC 개발하듯 진행할 수 있다.

SpringBoot + Spring Data JPA + GraphQL을 이용한 프로젝트다.  
Spring for GraphQL은 Spring Boot 2.7.0 버전 이상부터 지원한다고한다.  

<br/>  

GraphQL을 통해 요청을 보낸다음   
Spring data JPA로 임베디드 H2 데이터베이스에 접근하여   
데이터를 조회하고 저장하는 테스트를 진행할 것이다.  

<br/>

#### 프로젝트 셋팅    

셋팅에 앞서 모든 셋팅을 다올리진 않을 것이다.   
글의 가독성 측면이나 현재 GraphQL 설명에 집중하기 위해서이다.   
_(프로젝트 생성에 다른 정보가 필요하다면 [GitHub 레포지토리]를 참고하자)_


<br/>   

**프로젝트 구조**  

```text
├── main
│   ├── java
│   │   └── com
│   │       └── prac
│   │           └── graphql
│   │               ├── GraphqlApplication.java
│   │               ├── domain
│   │               │   ├── controller
│   │               │   │   └── MemberController.java
│   │               │   ├── dto
│   │               │   │   └── MemberResponseDto.java
│   │               │   ├── entity
│   │               │   │   └── Member.java
│   │               │   ├── mapper
│   │               │   │   └── MemberMapper.java
│   │               │   ├── repository
│   │               │   │   └── MemberRepository.java
│   │               │   └── service
│   │               │       └── MemberService.java
│   │               └── global
│   │                   └── exception
│   │                       ├── BusinessLogicException.java
│   │                       ├── ExceptionCode.java
│   │                       └── handler
│   │                           └── GraphQLExceptionHandler.java
│   └── resources
│       ├── application.yml
│       ├── data.sql
│       ├── graphql
│       │   └── schema.graphqls
│       ├── static
│       └── templates
└── test
    └── java
        └── com
            └── prac
                └── graphql
                    └── GraphqlApplicationTests.java

```

<br/>

**build.gradle**

```java
dependencies {
        // ... 생략
        
        implementation 'org.springframework.boot:spring-boot-starter-graphql' // <- graphql
        testImplementation 'org.springframework:spring-webflux' // <- graphql
        testImplementation 'org.springframework.graphql:spring-graphql-test' // <- graphql
        
        // ... 생략
}
```
`Spring for GraphQL` dependencies를 추가하면 옆에 주석처리해둔 3가지가 생긴다. 

<br/>  


**application.yml**

```yaml
spring:
  graphql:
    graphiql:
      enabled: true # Graphql 테스트가 가능해진다. localhost:8080/graphiql 에 접속해서 가능 
      printer: 
        enabled: true # JPA에 Show-sql과 같이 Graphql 쿼리를 출력해준다.
```

**application.properties**

```properties
spring.graphql.graphiql.enabled=true
spring.graphql.graphiql.printer.enabled=true
```

`.yml`,`properties`파일 셋팅 방법이다.    
나는 `.yml`파일을 사용하였고 이외에 H2, Spring data JPA 관련 설정은   
위에 올려둔 깃허브 레포지토리를 참고하자  

<br/>  

코드를 보기전에 아래의 3개 어노테이션의 용도를 알고있으면   
도움이되니 먼저 읽어보고 코드를 확인하자

**@MutationMapping**  
`@MutationMapping`은 Create, Update, Delete에 대응된다고 생각하면 될 것 같다.  
graphql은 endpoint가 하나이므로 @MutationMapping 어노테이션만 지정해 주고 다른 설정은 필요 없습니다.  
즉, @PostMapping, @PatchMapping, @DeleteMapping 등을 대신사용

<br/>

**@QueryMapping**  
`@QueryMapping`는 Read에 대응된다고 생각하면 될 것 같다.  
말고도 `@SubscriptionMapping`이 있다고 한다.  
즉, @GetMapping 을 대신 사용

<br/>

**@SchemaMapping**   
위의 두개의 어노테이션을 통용해서 쓰는듯한 느낌이다.   
typeName(Query, Mutation)을 설정하여 사용이 가능하다.

<br/>   

또한 `spring-boot-starter-graphql`를 사용하는 경우 default로 설정된    
Schema 파일 위치는 `classpath:graphql/**/` 으로 `/src/main/resources/graphql/`    
경로에 `schema.graphqls` 파일을 만들어 사용할 수 있다. (graphql가 아닌 graphqls이다)

**schema.graphqls**

```graphql
type Member{
    id: ID!
    name: String!
    age: Int!
}

type MemberResponseDto{
    id: ID!
    name: String!
    age: Int!
}

# @QueryMapping 메서드 이름과 동일
# value 속성을 통해서 이름을 정해줄 수 있음
type Query{
    getMember(id: ID!): Member
    getMembers: [Member]

    getMemberResponseDto(id: ID!): MemberResponseDto
    getMembersResponseDto: [MemberResponseDto]

    getMemberBySchema(id: ID!): Member
    getMembersBySchema: [Member]
}

# @MutationMapping 메서드 이름과 동일
# value 속성을 통해서 이름을 정해줄 수 있음
type Mutation {
    postMember(
        id : Int
        name : String
        age : Int
    ): Member
    postMemberByMutation(
        id : Int
        name : String
        age : Int
    ): Member
}
```

<br/>    

**Member.java**

```java
@Entity
@Builder
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private Integer age;
}
```

<br/>  
 
**MemberService.java**  
```java
@Service
@Transactional
@RequiredArgsConstructor
public class MemberService {

    private final MemberRepository memberRepository;

    public Member findMemberById(Long id) {
        Optional<Member> findMember = memberRepository.findById(id);
        return findMember.orElseThrow(() -> new BusinessLogicException(ExceptionCode.MEMBER_NOT_FOUND));
    }

    public List<Member> findAllMember() {
        return memberRepository.findAll();
    }

    public Member saveMember(Member member) {
        return memberRepository.save(member);
    }

}
```

<br/>  

**MemberRepository.java**

```java
public interface MemberRepository extends JpaRepository<Member, Long> {
}

```

<br/> 

**MemberMapper.java**

```java
@Component
public class MemberMapper {
    public static MemberResponseDto memberToMemberResponseDto(Member member) {
        return MemberResponseDto.builder()
                .id(member.getId())
                .name(member.getName())
                .age(member.getAge())
                .build();
    }

    public static List<MemberResponseDto> memberListToMemberResponseDtoList(List<Member> members) {
        return members.stream()
                .map(member -> {
                    return memberToMemberResponseDto(member);
                })
                .collect(Collectors.toList());
    }

    public static Member toEntity(String name, Integer age) {
        Member newMember = new Member();
        newMember.setName(name);
        newMember.setAge(age);
        return newMember;
    }
}
```

<br/>

위에까지는 REST API와 별반 다르지 않은 계층의 코드이다.   
Repository - Service 계층이고 Mapper를 이용해 맵핑을 도와주는 클래스다.    
Controller 부터는 조금 달라진 모습을 볼 수 있을 것이다.  

**MemberController.java**

```java
/*
* EndPoint : http://localhost:8080/graphql
* Test 접속 URL : http://localhost:8080/graphiql 에 쿼리를 입력
* */
@Controller
@RequiredArgsConstructor
public class MemberController {

    private final MemberService memberService;
    private final MemberMapper memberMapper;

    /*
    * @QueryMapping
    * -> 주로 Read에 사용됩니다. (REST API @GetMapping과 대응)
    * */

    @QueryMapping
    public Member getMember(@Argument Long id){
        return memberService.findMemberById(id);
    }

    @QueryMapping
    public List<Member> getMembers(){
        return memberService.findAllMember();
    }

    @QueryMapping
    public MemberResponseDto getMemberResponseDto(@Argument Long id){
        Member member = memberService.findMemberById(id);
        return memberMapper.memberToMemberResponseDto(member);
    }

    @QueryMapping
    public List<MemberResponseDto> getMembersResponseDto(){
        List<Member> allMember = memberService.findAllMember();
        return memberMapper.memberListToMemberResponseDtoList(allMember);
    }

    /*
    * @MutationMapping
    * -> 주로 Create, Update, Delete에 사용됩니다.
    * */
    @MutationMapping
    public Member postMember(@Argument String name,
                             @Argument Integer age){

        Member member = memberMapper.toEntity(name, age);
        return memberService.saveMember(member);
    }

    /*
    * @SchemaMapping
    * -> @QueryMapping, @MutationMapping 를 선택하여 사용할 수 있음.
    * */
    @SchemaMapping(typeName = "Query")
    public Member getMemberBySchema(@Argument Long id) {
        return memberService.findMemberById(id);
    }

    @SchemaMapping(typeName = "Query")
    public List<Member> getMembersBySchema() {
        return memberService.findAllMember();
    }

    @SchemaMapping(typeName = "Mutation")
    public Member postMemberByMutation(@Argument String name,
                                       @Argument Integer age){
        Member member = memberMapper.toEntity(name, age);
        return memberService.saveMember(member);
    }

}
```
첫번쨰로 어노테이션들이 기존에 사용하던 Mapping어노테이션과는 다르고   
@ReqeustBody와 같은 어노테이션도 사용하지 않는 모습이다.   
반환타입도 `schema.graphqls`에 지정되어있는 클래스들을 반환 타입으로 사용하였고   

메서드 이름을 통해 Query,Mutation에 접근하는 형태를 가지고있다.   
value라는 속성값을 이용하면 ex) @MutationMapping(value = "postMember")   
메서드 이름이아닌 지정한대로 변경도 가능하다.   

REST API와 어노테이션과 반환형식만 다르지   
크게 코드적으로 어렵게 작성해야하는 부분은 없다.    
이런 기능을 제공해주는 Spring에게 감사를,,,   

<br/>   

#### 테스트 진행   

이제 위와 같이 프로젝트 셋팅이 끝났으면   
Controller에 작성한 API를 검증할 수 있다.   

Spring Boot를 실행하면 

![image](https://user-images.githubusercontent.com/95069395/231749021-722b6320-da74-4163-b506-09102951b10b.png){: .align-left style="max-width: 100%"}  

위 사진과 같이 GraphQL endpoint가 표시되면 정상적으로 실행된 것이다.   
로컬환경에서 API 요청시 `localhost:8080/graphql`로 요청을 진행하면된다.   

<br/>  

실제 API를 테스트하는 방법은 2가지 정도 있다.

**1). Postman을 통한 요청**   

![image](https://user-images.githubusercontent.com/95069395/231749779-9bdc4a6d-7094-44cb-b6f5-90718bce5139.png){: .align-left style="max-width: 100%"}  

포스트맨으로 위에 앤드포인트로 요청하면    
위와 같이 정상적으로 작동하는지 확인이 가능하다.    

문법 자동완성이되지 않아 2번방법을 추천한다.

<br/>   

**2). graphiql을 통한 요청**

브라우저에 `localhost:8080/graphiql`를 접속하면된다.   
`i`가 추가되었으니 이점을 꼭 참고하자  

![image](https://user-images.githubusercontent.com/95069395/231749144-a022257c-8461-4fcf-958c-52b63ec70b0b.png){: .align-left style="max-width: 100%"}  

접속하게되면 위와 같이 쿼리를 입력하고 오른쪽에는 출력이된 화면이 표시된다.   


실제로 요청 쿼리를 만들어 보냈을 경우

![image](https://user-images.githubusercontent.com/95069395/231749203-7dd0f47b-d09e-4ed5-afec-9f5efa09089d.png){: .align-left style="max-width: 100%"}

위에 처럼 확인이 가능한 모습을 볼 수 있다.   
프론트쪽에서 원하는 데이터를 요청하는 것들만 만들어서   
반환하는 특성을 확인해 볼 수 있다.   

<br/>   


그럼 실제로 우리가 위에서 만든 Controller를 테스트하기 위해서   
아래와 같은 쿼리문이 필요하다.

```roomsql
query{
    getMember(id:1) {
      id
      name
      age
    }
    getMembers {
      id
      name
    }
    getMemberResponseDto(id :1) {
      id
      name
      age
    }
    getMembersResponseDto {
      id
      name
    }
    getMemberBySchema(id :1) {
      id
      name
      age
    }
    getMembersBySchema {
      id
      name
    }
}
```

총 6개 API를 호출하는 테스트이다.   

<br/>   

또한 POST를 해보기 위해서   
mutation를 만들어서 실제로 Database에 저장되는지도 확인해보았다.   


```roomsql
mutation{
    postMember(name:"미노이", age:18){
        id
        name
        age
    }
    postMemberByMutation(name:"김채원", age:19){
        id
        name
        age
    }
}
```
`@Argument`로 받을 매개변수 설정(name, age)하여   
id,name,age를 반환받는다고 보면 될 것 같다.   

<br/>   

---

이렇게 오늘은 REST API대신에 GraphQL이라는 새로운 방식을   
공부해보았다. 처음에 생소해서 공식문서랑 여러 블로그를 뒤져가면서 공부했는데    
직접 코드를 작성하면서 체득하는 것이 확실히 이해하는데 빠른 것 같다.





<br/>
<br/>

**✨ 참고 블로그**  

- [graphql 공식문서]
- [hahahoho5915님 블로그 - 깃레포참고]
- [danawalab님 블로그]
- [siyoon210님 블로그]
- [jyleedev님 블로그]
- [kotlinworld님 블로그]

<br/>
<br/>


[GitHub 레포지토리]: https://github.com/MyCatlikesChuru/graphql-practice
[graphql 공식문서]:https://www.graphql-java.com/tutorials/getting-started-with-spring-boot
[danawalab님 블로그]:https://danawalab.github.io/spring/2022/06/06/Spring-for-GraphQL.html   
[hahahoho5915님 블로그 - 깃레포참고]:https://hahahoho5915.tistory.com/56
[siyoon210님 블로그]:https://siyoon210.tistory.com/153
[jyleedev님 블로그]:https://velog.io/@jyleedev/Spring-GraphQL-Postman-%EC%98%88%EC%A0%9C-%EC%8B%A4%EC%8A%B5 
[kotlinworld님 블로그]:https://kotlinworld.com/330