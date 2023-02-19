---
layout: post
title: "[Spring] 단위 테스트 JPA metamodel must not be empty!"
subtitle: 
categories: Spring
tags: [Spring, JUnit]
comments: true
published: true
---


### ✅ 문제발생 및 원인

Controller 단위 테스트를 진행하려고 `@WebMvcTest`  
어노테이션을 실행 시켰더니 아래와 같은 에러가 발생했다.  

```text
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jpaAuditingHandler': Cannot resolve reference to bean 'jpaMappingContext' while setting constructor argument; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jpaMappingContext': Invocation of init method failed; nested exception is java.lang.IllegalArgumentException: JPA metamodel must not be empty!
```

원인을 파악중 Entity들의 생성,수정 LocalDateTime을 자동으로  
관리해주는 `@EnableJpaAuditing`어노테이션을 `@SpringBootApplication`클래스에  
등록해 놓은 거이 위의 에러의 요인이였다.  

```java
@EnableJpaAuditing
@SpringBootApplication
public class JaritalkApplication {

	public static void main(String[] args) {
		SpringApplication.run(JaritalkApplication.class, args);
	}
}
```

`@EnableJpaAuditing`은 JPA관련 Bean들을 필요로하는데   
`@WebMvcTest`같은 단위테스트는 JPA관련 Bean들을 로드하지 않기 때문이다.  


<br/> 

### 📌 해결 방법   

**1. Configuration 분리**  

```java
@Configuration
@EnableJpaAuditing
public class JpaAuditingConfiguration {  
}
```

`@EnableJpaAuditing` 어노테이션을 새로운 클래스를 만들어  
빈으로 등록해주면 된다.  

<br/>  

**2. MockBean 추가**  

```java
@MockBean(JpaMetamodelMappingContext.class)
@WebMvcTest(controllers = CommunityController.class)
class CommunityControllerTest {
    ...
}
```
테스트하려고 하는 클래스에 `JpaMetamodelMappingContext`   
`@MockBean`을 추가해주면된다.  







[문제 해결 참고 블로그]: https://xlffm3.github.io/spring%20&%20spring%20boot/JPAError/