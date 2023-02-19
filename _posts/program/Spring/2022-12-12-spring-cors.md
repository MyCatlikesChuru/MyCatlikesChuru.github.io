---
layout: post
title: "[Spring] CORS 설정하기"
subtitle: 
categories: Spring
tags: [Spring, cors]
comments: true
published: true
---

오늘은 CORS에 대한 내용을 간단하게 적어보려한다.

<br/>

[TODO List 연습 클라이언트]를 이용해 로컬톰켓서버와  
연결 시 CORS문제가 발생해 해결했던 코드를 올려 본다.


**1). 어노테이션으로 설정하는 방법**
```java
@CrossOrigin(origins = "https://todobackend.com")
@RestController
public class TodoListController {
    ...
}
```



<br/>

**2). 빈 객체를 만들어 설정하는 방법**

```java
@SpringBootApplication
public class TodolistApplication {

	public static void main(String[] args) {
		SpringApplication.run(TodolistApplication.class, args);
	}
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/**").allowedOrigins("https://todobackend.com/");
			}
		};
	}
}
```






[TODO List 연습 클라이언트]: https://todobackend.com