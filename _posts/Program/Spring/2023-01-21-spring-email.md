---
layout: post
title: Spring) MailAuthenticationException 문제 해결
subtitle: 
categories: Spring
tags: [Spring, email]
comments: true
published: true
---

오늘은 회원가입시 이메일 전송 로직을 확인중  
`org.springframework.mail.MailAuthenticationException`와 같은  
예외를 만났고 해당관련 해결방법을 적어가보려한다.

<br/>

### ✅ 작업환경

```text
Spring Boot 2.7.7  
Java 11 
macOs Monterey 12.6 버전 
로컬환경에서 테스트 진행
```


<br/>

### 📌 에러 확인 및 해결

첫번째로 Spring 에서 지원하는 mail 서비스를 이용해보려한다.   
메일을 보내는 주체는 Google의 gmail을 이용하려한다.


```java
implementation 'org.springframework.boot:spring-boot-starter-mail'
```
[org.springframewokr.mail], [org.springframewokr.javamail] 패키지를 사용한다.  

`JavaMailSender` 인터페이스의 `send();`메서드를  
Override 하여 구현하였고 테스트 진행도중 아래와 같은 예외를 만나게 되었다.


```text
2023-01-21 15:40:59.934 ERROR 281 --- [         task-1] .a.i.SimpleAsyncUncaughtExceptionHandler : Unexpected exception occurred invoking async method: public void codestates.frogroup.indiego.global.email.event.MemberRegistrationEventListener.listen(codestates.frogroup.indiego.global.email.event.MemberRegistrationApplicationEvent) throws java.lang.Exception

org.springframework.mail.MailAuthenticationException: Authentication failed; nested exception is javax.mail.AuthenticationFailedException: 534-5.7.9 Application-specific password required. Learn more at
534 5.7.9  https://support.google.com/mail/?p=InvalidSecondFactor t1-20020a63d241000000b004c974bb9a4esm11189895pgi.83 - gsmtp
```

내용을 잘 읽어보니, 인증에 실패하였고  
`Application-specific password required` 애플리케이션의 스펙에 맞는  
비밀번호를 요구한다고한다. 그리고 참고용 주소를 하나가 콘솔에 찍혀있다.  
[앱 비밀번호로 로그인]관련 주소이며, 정말 친절한 Spring... 감동..

예외가 발생한 코드를 쭉 따라가다 보면  
`JavaMailSender`인터페이스의   

```java
	protected void doSend(MimeMessage[] mimeMessages, @Nullable Object[] originalMessages) throws MailException {
		Map<Object, Exception> failedMessages = new LinkedHashMap<>();
		Transport transport = null;

		try {
			for (int i = 0; i < mimeMessages.length; i++) {

				// Check transport connection first...
				if (transport == null || !transport.isConnected()) {
					if (transport != null) {
						try {
							transport.close();
						}
						catch (Exception ex) {
							// Ignore - we're reconnecting anyway
						}
						transport = null;
					}
					try {
						transport = connectTransport();
					}
					catch (AuthenticationFailedException ex) {
						throw new MailAuthenticationException(ex);
					}
					catch (Exception ex) {
						// Effectively, all remaining messages failed...
						for (int j = i; j < mimeMessages.length; j++) {
							Object original = (originalMessages != null ? originalMessages[j] : mimeMessages[j]);
							failedMessages.put(original, ex);
						}
						throw new MailSendException("Mail server connection failed", ex, failedMessages);
					}
				}
        }        
                
                ... 생략
	}
```
구현체인 `JavaMailSenderImpl`의 아래의 메서드에서   
`connectTransport();` 메서드를 호출할 때 예외가 발생한 것을 볼 수 있다.  

```java
	protected Transport connectTransport() throws MessagingException {
		String username = getUsername();
		String password = getPassword();
		if ("".equals(username)) {  // probably from a placeholder
			username = null;
			if ("".equals(password)) {  // in conjunction with "" username, this means no password to use
				password = null;
			}
		}

		Transport transport = getTransport(getSession());
		transport.connect(getHost(), getPort(), username, password);
		return transport;
	}
```
해당 코드에서도 `transport.connect();` 메서드 안에서  
host, port, user, password의 정보로 연결 시도를 하는데 정보가 맞지않아   
연결이 정상적으로 실행되지 않을 경우에 `AuthenticationFailedException` 예외가   
발생하는 거이라고 추측이된다... 좀더 어드벤스한 공부는 추후해 해봐야할 것 같다.


<br/>

그래서 스프링에서 친절하게 알려준 콘솔내용대로 해결을 해보자.

현재 yml 설정 파일이다.

```yaml
mail:
  smtp:
    host: smtp.gmail.com
    port: 587
    username: ${GOOGLE_ID}
    password: ${GOOGLE_PASSWORD}
    auth: true
    starttls:
      enable: true
  subject:
    member:
      registration: 개굴단의 인디고 서비스에 성공적으로 가입하셨습니다.
  template:
    name:
      member:
        join: email-registration-member
``` 
환경변수를 이용해 설정해두었고  
username에는 구글 이메일 주소를 넣었고  
password에는 구글 비밀번호를 넣었었다.   

여기서 비밀번호 스펙이 맞지않다고 예외가 발생한 것이였고  
[앱 비밀번호로 로그인]으로 접속해 앱 비밀번호를 설정해주었다.   

![image](https://user-images.githubusercontent.com/95069395/213848506-5245e665-d3ef-4c2d-901d-94c49ef6f696.png){: .align-left style="max-width: 100%"}

최종적으로 비밀번호를 받게된다면  
16자의 비밀번호를 발급받게 될 것이고   

password 환경변수에 공백없이 16자를 입력해주면 된다.  
ex) abcdefghijklmnop

<br/>

수정을 하고 다시 회원가입 서비스를 진행해보았다.

![image](https://user-images.githubusercontent.com/95069395/213848511-98c77ad0-50b7-4470-8665-538d73155427.png){: .align-left style="max-width: 100%"}

간단한 템플릿으로 우선 테스트를 진행했고   
회원가입을한 유저의 메일로 정상적으로 회원가입 안내 메일이 보내진 것을 확인해볼 수 있다!!  



<br/>
<br/>

✨ 참고 블로그  
[문제 해결 참고 블로그]  

<br/>
<br/>

[org.springframewokr.mail]: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/mail/package-summary.html
[org.springframewokr.javamail]: https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/mail/javamail/package-summary.html
[앱 비밀번호로 로그인]: https://support.google.com/accounts/answer/185833?visit_id=638098789197662028-710657186&p=InvalidSecondFactor&rd=1
[문제 해결 참고 블로그]: https://kijuk.tistory.com/162?category=1056346