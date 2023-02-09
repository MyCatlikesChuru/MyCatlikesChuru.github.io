---
layout: post
title: H2 Database 다운로드 및 실행
subtitle: 
categories: DB
tags: [DB, H2]
comments: true
published: true
---

오늘은 로컬에서 테스트할때 자주 사용하는  
H2 Database를 다운받아 설정해보려한다.  


[h2 database 홈페이지]에 접속하면  

![image](https://user-images.githubusercontent.com/95069395/217752289-86f04451-8164-49df-b9de-826ba6bf5264.png){: .align-left style="max-width: 100%"}

위와 같은 화면이 나오는데  
나는 MacOS를 쓰고있어 All Platforms를 눌러주면된다.  
그럼 zip파일 하나가 다운받아진다.   
여기서 버젼확인을 꼭해주자. 의존라이브러리를 추가할때 해당버젼과 일치해야한다.

**Gradle의 경우**  
```java
runtimeOnly 'com.h2database:h2:2.1.214'
```
와 같이 설정해 줄 수있다. 일반적으로 버전을 제외하고 
```java
runtimeOnly 'com.h2database:h2
```
로 설정하면 자동으로 가장 최신의 버전이 추가되는 것 같다.

<br/>

**Maven의 경우**  
```html
    <dependencies>
        <!-- H2 데이터베이스 -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>2.1.214</version>
        </dependency>
    </dependencies>
```
와 같은식으로 설정해줄 수 있다.


해당 zip파일을 풀면 폴더하나가 나오게되는데  
그안에서 bin -> h2.sh 스크립트를 실행 시켜주면된다.

![image](https://user-images.githubusercontent.com/95069395/217752303-1d0737ec-84df-4fcc-8c05-5e5d12c3a90e.png){: .align-left style="max-width: 100%"}

해당 파일 위치에서  
```shell
./h2.sh
```
를 실행 시켜주면 h2 data base 팝업이 발생하고    
![image](https://user-images.githubusercontent.com/95069395/217752305-2b795af1-57d9-426d-965a-50e9fbdc42c6.png){: .align-left style="max-width: 100%"} 아이콘이 상단에 떠있을 것이다.

만약 종료시키고 싶으면 `Ctrl + C`를 눌러주면된다.  

만약 스크립트가 권한에 의해 실행되지 않는다면 chmod 명령어로 권한을   
부여해주면 쉽게 해결할 수 있다.  
```shell
ll
```
권한 확인 진행을 먼저해주고  

```shell
chmod 755 h2.sh
```
를 해주게되면 정상적으로 진입할 수 있을 것이다.

![image](https://user-images.githubusercontent.com/95069395/217752309-02ac4522-d3df-45d1-8137-7398a77a51bb.png){: .align-left style="max-width: 100%"}

접속 URL 같은 정보들의 경우는  
gradle과 maven에 따라다르지만 resources에 정보를 설정해주면된다.  
혹시나 만약 해당 화면이 발생하지 않고 하얀화면만 나오면서  
진입이 안되는 분이 있으시다면  
포트번호 `:8082`앞에 localhost를 붙여보면 접속이 가능할 것이다.


gradle 같은 경우에는  
```properties
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
spring.datasource.username=sa
spring.datasource.password=
```
application.properties 설정을 통해  
접속 관련 정보를 설정해주면 된다.    

<br/>

Maven 같은 경우에도 마찬가지다
```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
        </properties>
    </persistence-unit>
</persistence>
```
과 같은 방식으로 `persistence.xml` 설정을 통해  
정보를 입력해줄 수 있다. 

[h2 database 홈페이지]: http://www.h2database.com/html/main.html