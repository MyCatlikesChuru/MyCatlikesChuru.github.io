---
layout: post
title: Spring) AWS S3 FileUpload 하여 URL로 관리하기  
subtitle: 
categories: Spring
tags: [Spring, S3]
comments: true
published: true
---

오늘은 AWS S3에 파일 업로드를 하는  
예제를 다뤄보려한다.  

구글에서 여러가지 블로그를 돌아다니며 학습했고  
순서와 코드에 대해 정리해보려한다.   
내가 작성한 코드는 [GitHub 레포지토리]로 공유해본다.

<br/>

### ✅ 작업 환경  

```text
AWS S3 버킷
Spring Boot 2.7.7  
Java 11 
macOs Monterey 12.6 버전 
로컬환경에서 테스트 진행
```

<br/>


### 📌 작업 순서 및 준비

내가 구상하려고 생각하는 아키텍처를 그려보았다.  
순서를 간단히 짚고 넘어가자

![image](https://user-images.githubusercontent.com/95069395/212537839-f1ebee39-3c06-449e-b3f0-359bb3c3d1c6.png){: .align-left style="max-width: 100%"}


1). 클라이언트에서 이미지파일을 업로드 요청  
-> multipart/form-data 타입으로 요청  
-> Key=file / value=이미지 형태로 요청을 보냄  

2). 요청을 받은 서버에서 파일을 AWS S3 버킷에 저장함.  
-> 전달 받은 파일을 AWS 저장소에 원하는 폴더에 저장함.  
-> 저장완료후 DataBase에 URL을 저장함.  

3). 저장이 정상적으로 처리되었다고 클라이언트에 응답함.  
-> 만약 저장에 실패 및 예외가 발생했을 때는 예외처리하여 응답해줌  

❗️코드가 길어질 것을 우려해 DB에 저장하는 과정은 생략합니다!  
AWS S3에 저장되고 URL을 추출하는 과정만 담으려합니다.

<br/>

### 📌 AWS S3 생성  

첫번째로 해야할 것이 S3 버킷을 생성해야한다.   
여러가지 글을 참고해보면 쉽게 만들 수 있을 것이다.   
그래도 이러한 과정을 적어보려한다.   

나와 같은 경우는 프리티어 계정이 있기에 무료로 만들어 사용했다.   
무료 기준에 넘어가지만 않게 잘 사용하면 무리는 없을 것 같다.   

본격적으로 만들어보자   


![image](https://user-images.githubusercontent.com/95069395/212538169-ec74cc60-1060-40d0-8e44-df280f9571dc.png){: .align-left style="max-width: 100%"}

버킷 이름과 리전을 선택해주자.  
한국에 있기때문에 서울리전을 선택했다.  

<br/>

![image](https://user-images.githubusercontent.com/95069395/212538230-331609e3-66e3-4d2d-8f55-aa7cb22b4792.png){: .align-left style="max-width: 100%"}

우선 엑세스 차단의 체크박스는 해제시켜두었고  
이에따라 아래 경고문구가 뜬다. 해당부분을 체크해줘야  
버킷을 만들 수 있다.  

이미지를 클라이언트에 공용으로 사용할 것이기 때문에   
우선은 퍼블릭 액세서를 차단하지 않았다.  

<br/>


![image](https://user-images.githubusercontent.com/95069395/212538236-33952455-c626-4cad-99df-69af51b0ecf2.png){: .align-left style="max-width: 100%"}

![image](https://user-images.githubusercontent.com/95069395/212538244-c3dbfff3-1865-4a8f-9b49-1874cd5ca7c0.png){: .align-left style="max-width: 100%"}

이외 설정은 기본으로 두고 버킷 생성을 진행하면   
버킷이 만들어진 것을 볼 수 있다.  

해당 버킷에서 폴더를 만들어 관리를 할 수 있다.  
나는 프로필사진을 모아놓는 폴더에   
이미지 업로드한 파일을 관리하려한다.   

![image](https://user-images.githubusercontent.com/95069395/212538533-35027900-bebf-458b-b258-bd28fe7a5025.png){: .align-left style="max-width: 100%"}

버킷에서 폴더 만들기를 눌러 만들어 줄 수 있다.

![image](https://user-images.githubusercontent.com/95069395/212538576-c6629514-ad7d-406f-9c39-3b9ce24562e8.png){: .align-left style="max-width: 100%"}

만들게될 경우 위와 같은 폴더가 생기는 것을 볼 수 있다.   
최종적으로 위에 폴더에 이미지를 올려볼 예정이다.

<br/>

### 📌 IAM 설정

IAM -> 엑세스 관리 -> 사용자 -> 사용자 추가를 눌러 줍니다.  


![image](https://user-images.githubusercontent.com/95069395/212538540-44bf93ef-9803-4f48-9f86-827a3d36f4fe.png){: .align-left style="max-width: 100%"}

사용자이름을 원하는대로 설정하고   
엑세스 키 체크 박스를 체크한다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/212538545-cdcaad32-3f64-4a44-b73d-6c534338cb6b.png){: .align-left style="max-width: 100%"}

다음으로 넘어가 기존 정책 직접 연결에서  
`AmazonS3FullAcess`를 추가해준다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/212538553-619d2f40-f900-4376-a9f7-c79633571007.png){: .align-left style="max-width: 100%"}

다음을 눌러 넘어가준다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/212538556-b8fff09d-fe94-4dc2-bdfc-58f3625efee0.png){: .align-left style="max-width: 100%"}

사용자 만들기 추가

<br/>


![image](https://user-images.githubusercontent.com/95069395/212538557-7d71c867-db35-4be6-bd34-a1b9fc4a99e9.png){: .align-left style="max-width: 100%"}

지정한 사용자이름으로 엑세스 키ID와 비밀 엑세스 키가 발급된다.   
이 두가지가 중요하다. 후에 연결할때 사용되니 따로 저장해두자   

이 키는 절대로 Github에 올리거나 공유되면 안되는 키값이니   
주의해서 관리하도록 하자

<br/>


![image](https://user-images.githubusercontent.com/95069395/212538560-2247c35c-3d06-46c3-b4ef-215f9e8ca6ac.png){: .align-left style="max-width: 100%"}

최종적으로 IAM 사용자가 만들어진 것을 확인할 수 있다.   
해당 키값으로 S3에 접근이 가능해졌고   
이제 코드를 작성해 이미지를 올려보도록 하자  

<br/>

### 📌 코드 작성   

#### 🛠 기본 셋팅

`application.yml` 을 설정해주자

```yaml
spring:
  servlet:
    multipart:
      max-file-size: 10MB
      max-request-size: 10MB
cloud:
  aws:
    credentials:
      access-key: ${S3_ACCESS_KEY}
      secret-key: ${S3_SECRET_KEY}
    s3:
      bucket: indiego-fileupload/profileimage
    region:
      static: ap-northeast-2
    stack:
      auto: false
```

처음으로는 servlet의 multipart 용량 사이즈를 지정해주었다.   
10MB 이상을 넘지못하도록 지정했고, 넘은 파일이 올 경우에  
예외처리는 가장 맨 아래에서 알아보자

위에서 S3 설정했던 것과 같이   
region은 서울(ap-northeast-2)로 지정  
bucket은 나의 버킷이름인 indiego-fileupload에   
폴더 경로인 /profileimage를 붙여주었다.  

그리고 acees-key, secret-key에 대한 설정은   
환경변수로 사용할 수 있고 지정해두었다. 각자 환경에 맞게  
값을 환경변수에 넣고 사용하거나 하드코딩으로 해두어도된다.(깃에올리지만 말것..)  

<br/>  

그리고 build.gradle을 설정해주자.

```java
dependencies {
        ...
	implementation 'org.springframework.cloud:spring-cloud-starter-aws:2.2.6.RELEASE'
        ...
}
```
기본적인 Spring web, Lombok 같은   
의존 라이브러리도 설정해주어야한다.

다음으로는  
AmazonS3Config 클래스를 작성해보자.

```java
@Configuration
public class AmazonS3Config {
    
	@Value("${cloud.aws.credentials.access-key}")
	private String accessKey;

	@Value("${cloud.aws.credentials.secret-key}")
	private String secretKey;

	@Value("${cloud.aws.region.static}")
	private String region;

	@Bean
	public AmazonS3Client amazonS3Client() {
		BasicAWSCredentials awsCreds = new BasicAWSCredentials(accessKey, secretKey);
		return (AmazonS3Client) AmazonS3ClientBuilder.standard()
			.withRegion(region)
			.withCredentials(new AWSStaticCredentialsProvider(awsCreds))
			.build();
	}
}
```

`AmazonS3Client`타입으로 빈을 만들어 주었다.   
이용된 정보는 환경변수에 설정했던 값들이 이용되었다.  
이렇게까지만 설정해놓고 스프링 부트가 잘 실행되는지 확인해보자  

<br/>

#### 🐞 문제발생 처리

실행중 여러가지 예외들을 만나게 될 수 있다.   
내가 만든 예외들의 해결방법을 적어본다.

**1). AmazonClientException 문제**

```java
com.amazonaws.AmazonClientException: EC2 Instance Metadata Service is disabled
```

위와 같은 예외가 콘솔에 찍혀있었다.  
[AmazonClientException 참고 레퍼런스]를 참고해서 해결해보았다.

![image](https://user-images.githubusercontent.com/95069395/212529483-83bdd3c5-1ec1-499e-a7b9-9e4d4143866b.png){: .align-left style="max-width: 100%"}

첫번째로 구성편집에서  VM Option을 추가해  
`-Dcom.amazonaws.sdk.disableEc2Metadata=true`를 추가해주었다.

그리고 yml 파일 로깅 설정을


```yaml
logging:
  level:
    com:
      amazonaws:
        util:
          EC2MetadataUtils: error
```

위와 같이 설정해준다면, 더이상 오류 메세지는 보이지 않을 것이다.  

<br/>

**2). AmazonS3Exception 문제**  

이번에 발생한 문제는 버킷에서 ACLs를 허용하지  
않았다는 문구가 출력외었다.

```shell
com.amazonaws.services.s3.model.AmazonS3Exception: The bucket does not allow ACLs
```  

해당 문제도 AWS S3 버킷의

![image](https://user-images.githubusercontent.com/95069395/212529451-bd253a0e-7033-44a9-9749-8704c9223db3.png){: .align-left style="max-width: 100%"}

ACL을 활성화시켜두었더니 해결되었다.

<br/>

#### 🛠 코드 작성  

첫번째로 Controller를 작성해보자  

```java
package com.image.fileupload.controller;

import com.image.fileupload.service.AwsS3Service;
import com.image.fileupload.service.ImageUploadService;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;

@Slf4j
@RestController
@RequestMapping("/upload")
public class UploadController {
    private final ImageUploadService awsS3Service;

    public UploadController(AwsS3Service awsS3Service) {
        this.awsS3Service = awsS3Service;
    }

    @PostMapping
    public String saveImage(@RequestParam MultipartFile file){

        log.info("multipartFile = {}", file);
        String url = awsS3Service.StoreImage(file);
        log.info("url = {}",url);
        return url;
    }
}
```
사이사이에 로그로 값을 확인할 수 있도록 찍어두었다.   
key = file / value = 이미지 로 받아오는 방법이다.  
Spring에서는 MultipartFile 형식으로 받아올 수 있다.   
물론 HttpServletReqeust에서도 꺼내올 수 있다.

여기서 MutipartFile 타입으로 받아온 file 객체를 로그로 찍어보면  
`StandardMultipartFile@57413913`와 같은 형태로 오는게 확인이 가능하다.

<br/>  

이제 서비스 로직을 작성해보자   
AWS S3 외에 다른 저장소를 사용할 수 있게에   
인터페이스를 하나 만들었다.   

```java
public interface ImageUploadService {
	String StoreImage(MultipartFile img);
}
```

해당 인터페이스를 구현체로   
AwsS3Service 클래스를 만들었다.

```java
@Slf4j
@RequiredArgsConstructor
@Service
public class AwsS3Service implements ImageUploadService {

	private final AmazonS3 amazonS3;

	@Value("${cloud.aws.s3.bucket}")
	private String bucketName;

	public String StoreImage(MultipartFile file) {
		validateFileExists(file);
		String originalFilename = file.getOriginalFilename();
		String storeFileName = createStoreFileName(originalFilename);

		try (InputStream inputStream = file.getInputStream()) {
			amazonS3.putObject(new PutObjectRequest(bucketName, storeFileName, inputStream, null)
				.withCannedAcl(CannedAccessControlList.PublicRead));
		} catch (IOException e) {
			throw new RuntimeException();
		}

		return amazonS3.getUrl(bucketName, storeFileName).toString();
	}

	private void validateFileExists(MultipartFile multipartFile) {
		if (multipartFile.isEmpty()) {
			throw new RuntimeException();
		}
	}

	private static String createStoreFileName(String originalFilename) {
		String ext = extractExt(originalFilename);
		String uuid = UUID.randomUUID().toString();
		return uuid + "." + ext;
	}

	private static String extractExt(String originalFilename) {
		int pos = originalFilename.lastIndexOf(".");
		return originalFilename.substring(pos + 1);
	}
}
```

여기서 AmazonS3 를 DI 받아 `.putObject();` 메서드를 이용해   
AWS S3 저장소에 저장을할 수 있게되어진다.   

`.putObject();`의 매개변수들로는  
(버킷 이름과, 파일이름, MultipartFile inputsStream 객체, ObjectMetaData 객체)  
를 넣어 주면 정상적으로 파일이 업로드가 되어질 것이다.   

그리고 외부에 공개할 이미지이기 때문에  
`.withCannedAcl(CannedAccessControlList.PublicRead)`를 걸어주었다.

나와 같은 경우에 파일이름같은 경우는   
기존에 받은 originalFilename을 UUID를 이용해   
종복될 확률이 매우 낮게 파일이름을 변경해주고 마지막에는  
확장자 데이터를 뽑아 붙여주게 되어 파일이름을 완성시켜주었다.   

예를들자면 `e491b9f6-c528-4a47-bd5c-0381aad21ed5.png`와 같이   
파일이름이 만들어지게 되는 모습을 볼 수 있다.

<br/>  

여기까지 작성을 헀다면 이제 클라이언트 쪽에서   
이미지를 보내 AWS S3에 저장되는 모습을 확인해볼 수 있다.

클라이언트을 SSR 방식으로  
thymeleaf를 이용해 만들어줄 수도 있지만

![image](https://user-images.githubusercontent.com/95069395/212540211-2ec688c8-b5c4-4630-8b45-56f08ae246f9.png){: .align-left style="max-width: 100%"}

포스트맨을 통해서도 전송이 가능하다.   
multipart/form-data 형식으로 설정해준다음.   
Body에 Key,Value를 설정해주면된다.   

Key값 설정시에는 오른쪽에 Text가아닌 File로 변경해주어야한다.  
그러면 Value에 이미지를 선택할 수 있는 창이나온다.   
창을 눌러 현재 데스크탑에서 올릴 이미지를 선택해준다.


그리고 API를 요청보내게 된다면

![image](https://user-images.githubusercontent.com/95069395/212540224-4a1655bc-b22f-4694-a948-dcb7d368e697.png){: .align-left style="max-width: 100%"}

내가 지정했던 indiego-fileupload 버킷에  
profileimage라는 폴더에 내가 지정한 형식의 폴더이름과 확장자로   
객체가 올라가있는 모습을 확인할 수 있다.


![image](https://user-images.githubusercontent.com/95069395/212540227-70c09e05-f781-4a21-9236-8f60b8872d04.png){: .align-left style="max-width: 100%"}

객체를 눌러 들어가면 객체 URL이 나오게되는데  
해당 URL을 꺼내서 클라이언트 측에 전달하고 우리 DB에 저장하면된다.   

꺼내는 코드는 이미 위에 작성되어있다.  
`amazonS3.getUrl(bucketName, storeFileName).toString();` 코드로   
`getUrl();` 메서드를 이용해 꺼내올 수 있다. 해당 값을 String으로   
반환해주고 해당값을 이제 우리가 원하는 목적에 맞게 사용하면된다.    

나와 같은 경우에는 반환한 URL을 text/plain으로 반환해주었고  
위에 Postman으로 보면 잘 반환된 것을 확인할 수가있다.


<br/>

### 📌 파일 사이즈 예외처리

처음에 설정했던 yml 파일에 10MB로  
용량을 제한해두었었다.

만약 정말로 사이즈가 넘게 된다면

```shell
org.springframework.web.multipart.MaxUploadSizeExceededException: Maximum upload size exceeded; nested exception is java.lang.IllegalStateException:
```

와 같은 예외가 발생하게 될 것이고   
해당 예외를 처리하기위해서 간단한 예외처리 로직을 구현해보았다.   

@ControllerAdvice를 이용해서  
`MaxUploadSizeExceededException`예외를 잡을 수 있다.

```java
@ControllerAdvice
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    protected ResponseEntity<ErrorResponse> handleException(MaxUploadSizeExceededException e) {
        log.error("MaxUploadSizeExceededException", e);

        log.info("# 예외처리 잡음");
        final ErrorResponse response = ErrorResponse.of(e);
        return ResponseEntity.internalServerError().body(response);
    }
}
```
```java
@Getter
@NoArgsConstructor
@JsonInclude(JsonInclude.Include.NON_NULL)
public class ErrorResponse {

	private int status;
	private String message;

	private ErrorResponse(String message, int status) {
		this.message = message;
		this.status = status;
	}

	public static ErrorResponse of(MaxUploadSizeExceededException maxUploadSizeExceededException) {
		return new ErrorResponse(maxUploadSizeExceededException.getMessage(),500);
	}

}
```

ErrorResponse에 대한 내용은 우선 대강 정의해보았다.   
어떻게 반환할지에대해서는 커스터마이징을 통해서   
입맛에 맞게 변경해보면 좋을 것 같다.

<br/>
<br/>


✨ 참고 블로그   
[AWS S3, FileUpload 참고 레퍼런스], [FileUpload 관련 참고 블로그 레퍼런스]

[AmazonClientException 참고 레퍼런스]: https://h-kkaemi.tistory.com/24
[AWS S3, FileUpload 참고 레퍼런스]: https://antdev.tistory.com/93#recentEntries
[FileUpload 관련 참고 블로그 레퍼런스]: https://victorydntmd.tistory.com/334  
[GitHub 레포지토리]: https://github.com/MyCatlikesChuru/ImageFileUpload