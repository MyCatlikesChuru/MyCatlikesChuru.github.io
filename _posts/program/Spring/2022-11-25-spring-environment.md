---
layout: post
title: Spring) Could not resolve placeholder '환경변수명' in value "${환경변수명}" 오류
subtitle: Mac 환경변수 적용안되는 문제
categories: Spring
tags: [Spring, zsh]
comments: true
published: true
---

오늘은 내가 겪은 환경변수에 관련된 설정과 오류를   
기록해보려고한다.  

<br/>

## 문제 내용  

우선 나는 Mac을 사용하고 Shell은 zsh를 사용한다.  

문제의 시작은 이러하다. JWT관련 공부중이였고  
코드에서 @Value 어노테이션으로 application.yml 파일의   
설정된 값들을 사용하려고하였다.

```java
@Component
public class JwtTokenizer {

    @Getter
    @Value("${jwt.secret-key}")
    private String secretKey;
    
    ... 이하 생략
}
```

사용하려고 하는 대상은 SecretKey이고  
중요한 정보이기 때문에 **시스템 환경변수**를 사용해  
값을 사용하고 싶어 .yml 파일에 값을 셋팅해두었다.  

<br/>

```yaml
jwt:
  secret-key: ${JWT_SECRET_KEY}       # 환경 변수로 설정했음
  access-token-expiration-minutes: 30
  refresh-token-expiration-minutes: 420
```

위와 같이 ${환경변수명} 으로 지정을하였고  
만약 테스트를 실행한다면 @Value가 .yml의 경로를 찾아  
JWT_SECRET_KEY라고 설정된 환경변수를 가져와야한다.


하지만 테스트를 실행했을때

```text
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'jwtTokenizer': Injection of autowired dependencies failed; nested exception is java.lang.IllegalArgumentException: Could not resolve placeholder 'JWT_SECRET_KEY' in value "${JWT_SECRET_KEY}"
```
위와 같은 에러가 발생했고.... 무언가 환경변수를  
제대로 가져오지 못한다고 생각이 들었다.

<br/>  

## 문제 확인 및 해결

첫번째로 해당 에러가 발생하는 대부분의 이유는
1. application.yml 혹은 application.properties를 설정하지 않거나 경로를 틀림 
2. 환경변수 설정에 문제가 있었음. (bash shell, zsh shell 등..)

하지만 나는 위에 있는 내용을 전부 충족했지만 왜? 도대체 값을 가져오지  
못하는지 미쳐버릴 지경에 이르렀었다.  

사실 문제 해결은 간단했다.  
하지만 문제 해결을 먼저 올리기전에 환경변수 설정에 대해 적어본다.

<br/>

### MacOS 환경 변수 적용법

**1). 현재 사용하고 있는 Shell을 확인**
```shell
echo $SHELL
```
사용하고 있는 터미널에서 위를 입력해준다.  

만약 zsh Shell을 사용하고 있을 경우에는  -> /bin/zsh  
bash Shell을 사용하고 있을 경우에는 -> /bin/bash  

아마 대부분 맥북유저는 zsh Shell을 사용하고 있을 것 같다.   

<br/>

**2). 사용하고 있는 Shell에 따른 등록**    

vi 편집기 사용법을 알고있다고 가정하겠습니다.
```shell
vi ~/.zshrc
```
만약 zsh를 사용하고 있는 경우에는 .zshrc 파일에 환경 변수를  
등록해줘야합니다.

<br/>

```shell
vi ~/.bashrc
```
bash를 사용하고 있는 경우에는 .bashrc 파일에 등록!

<br/>

**3). 환경 변수를 등록합니다.**

```shell
export JWT_SECRET_KEY=LeeJaehyeok637637123231231231123
```
현재 내가 등록하려는 환경변수는 `JWT_SECRET_KEY` 라는 이름으로  
위에서 설정한다고 얘기했었고, 해당 값은 위처럼 `LeeJaehyeok637637123231231231123`을  
사용하려고 값을 임의로 설정해서 넣어주었다.

<br/>

**4). 환경 변수 확인.**  
```shell
printenv
```
printenv 명령어를 입려하면 우선   
전체 적용된 환경변수 목록이 나온다.  
그 중에 우리가 적용한 JWT_SECRET_KEY가 있으면  
정상적으로 잘 적용되었다고 볼 수 있다.  

```shell
echo $JWT_SECRET_KEY
```
echo 명령어를 이용하면 지정해서 확인이 가능하다.  
내가 등록한 환경변수는 `JWT_SECRET_KEY`로 등록했기에  
해당 명령어를 입력하면 아래와 같이 표시된다면 정상이다.  

![image](https://user-images.githubusercontent.com/95069395/217752905-f7b69d86-b55d-45ba-9fa2-f31365620324.png){: .align-left style="max-width: 100%"}

그럼 여기까지 설정하고 조회까지 해보았으면  
환경변수 설정에는 문제가 없는 것이다.  

혹여나 bash 사용으로 bash_profile에 설정하셨는데  
환경변수가 자꾸 리셋된다는 문제를 겪는 분들은  
1번 항목에 어떤 Shell을 사용하는지 다시 확인해볼 필요가 있다.

<br/>    

### 그래서해결 방법?

문제는 너무 간단했다.  
실제로 환경변수는 잘 적용되어있었고  
환경변수가 적용되지 않았던 이유는 <span style="color:#F04965">**IDE Restart**</span>를 하지 않았던 문제였다.

![image](https://user-images.githubusercontent.com/95069395/217752907-41ce8961-0054-43c1-8103-29cfcca15954.png){: .align-left style="max-width: 100%"}

Restart 하는 방법은 먼저 상단의 `File`에서  
`Invaildate Caches`를 눌러준다.  


![image](https://user-images.githubusercontent.com/95069395/217752914-3e6eff77-ba43-4e05-a254-bd8b0e82208e.png){: .align-left style="max-width: 60%"}

그리고 `INVALIDATE AND RESTART` 버튼을 눌러주면 된다.  
그러면 IDE가 자동적으로 재시작될 것이고  
스프링 부트실행시 해당 에러는 발생하지않고, 정상적으로   
환경변수의 값을 가져오는 것이 확인이 가능하다.

실제로 출력해서 확인해보고 싶을 경우  
```java
@Component
public class JwtTokenizer {

    @Getter
    @Value("${jwt.secret-key}")
    private String secretKey;

    ... 중간 생략
    
    @PostConstruct
    public void init() {
        System.out.println(secretKey);
    }
    ... 이하 생략
}
```
Spring Boot 실행시 출력으로 값을 확인해 볼 수 있다.  


혹은 실제로 환경변수를 클래스를 이용해 가져오고 싶다면  

```java
@Component
public class JwtTokenizer {
    
    ... 이상 생략
            
    private final Environment environment;

    public JwtTokenizer(Environment environment){
        this.environment = environment;
        String key = environment.getProperty("JWT_SECRET_KEY");
        System.out.println(key);
    }
    
    ... 이하 생략
```
`import org.springframework.core.env.Environment;` 패키지를 improt하여  
`.getProperty();` 메서드로 환경변수를 가져올 수 있다.


<br/>

---

**기타 정보(Shell 변경)**

만약 zsh, bash를 변경해보고 싶으신 분은  
1). zsh를 기본으로 사용하고 싶은경우
```shell
chsh -s /bin/zsh
```

2). bash를 기본으로 사용하고 싶은경우
```shell
chsh -s /bin/bash
```
입력후 현재 노트북 비밀번호를 입력해주시면 됩니다.



---  
