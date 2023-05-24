---
layout: post
title: "Code Deploy 에러해결 : An error occurred (AccessDeniedException) when calling the CreateDeployment operation"
subtitle: 
categories: CI/CD
tags: [AWS, Code Deploy]
comments: true
published: true
---


### 🐞 에러 상황

Git Actions를 이용한 CI/CD 구성중 만난 에러다.   
에러 내용은 아래와 같다.

![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/31ea0235-3684-45f4-a9c1-ddcbe7ee9c4a){: .align-left style="max-width: 100%"}  

```text
Run aws deploy create-deployment --application-name *** --deployment-config-name CodeDeployDefault.AllAtOnce --deployment-group-name *** --s3-location bucket=***,bundleType=zip,key=travel-with-me-build.zip

An error occurred (AccessDeniedException) when calling the CreateDeployment operation: User: arn:aws:iam::***:user/s3-user is not authorized to perform: codedeploy:CreateDeployment on resource: arn:aws:codedeploy:ap-northeast-2:***:deploymentgroup:***/*** because no identity-based policy allows the codedeploy:CreateDeployment action
Error: Process completed with exit code 254.
```

에러 내용으로만 보았을때는 내가 생성한 사용자 IAM `s3-user`에 권한이 없어서 동작이 안되는 모습이다.    
(참고로 s3-user는 내가 생성한 IAM의 이름이다.)   

`AmazonEC2RoleforAWSCodeDeploy`과 `AWSCodeDeployRole` 권한 정책이 추가되어있는데   
Git Actions 실행시 왜 위와 같은 에러가 발생하는걸까?   

몇시간의 삽질 끝에 필자가 해결했던 방법을 올려본다.


<br/>

### 📌 해결 방법

우선 현재 내가 구성한 CI/CD 구조는   

GitActions -> S3 -> Code Deploy -> EC2로 빌드된 파일을   
알집으로 전송하고 해당 알집파일을 EC2에서 다운받고 실행시키는 구조이다.   

여기서 문제가 되는 부분은 S3까지는 정상적으로 알집파일이 전송된다.   
하지만 Code Deploy에서 S3의 알집파일을 EC2로 전송해야하는데    
위와 같은 권한이 없는 에러가 발생한 것이다.    

우선 내가 작성한 `gradle.yml` 파일을 공유해본다.  

```yaml
name: Java CI with Gradle

on:
  push:
    branches: [ "main" ]
    paths:
      - travel-with-me/** # 해당 폴더 변경 감지시
      - .github/** # github 하위 변경 감지시
      
permissions:
  contents: read

jobs:
  build:

    runs-on: ubuntu-latest
    
    defaults:
      run:
        working-directory: ./travel-with-me #폴더 경로 추가

    steps:
      # submodule을 사용하기 위한 토큰 추가 
      - name: Checkout
        uses: actions/checkout@v3
        with:
          token: ${{ secrets.ACTION_TOKEN }}
          submodules: true

      - uses: actions/checkout@v3
      - name: Set up JDK 11
        uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'temurin'

      # ~/gradlew' is not executable. 문제해결
      - name: Add permission
        run: chmod +x ./gradlew

      - name: Build with Gradle
        uses: gradle/gradle-build-action@67421db6bd0bf253fb4bd25b31ebb98943c375e1
        with:
          arguments: build
          build-root-directory: ./travel-with-me #폴더 경로 추가

      # build 후 압축하는 과정
      - name: Make zip file
        run: zip -r ./travel-with-me-build.zip .
        shell: bash

      # AWS 사용정보 입력
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY }} # IAM 권한 만들때 만든 엑세스키
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }} # IAM 권한 만들때 만든 시크릿키
          aws-region: ap-northeast-2 # 서울리전

      # 압축파일 AWS S3 버킷에 업로드(전달)
      - name: Upload to S3
        run: aws s3 cp --region ap-northeast-2 ./travel-with-me-build.zip s3://${{ secrets.S3_BUCKET_NAME }}/travel-with-me-build.zip

      # AWS Code Deploy 설정 및 실행
      - name: Code Deploy
        run: >
          aws deploy create-deployment --application-name ${{ secrets.CODE_DEPLOY_APPLICATION_NAME }}
          --deployment-config-name CodeDeployDefault.AllAtOnce
          --deployment-group-name ${{ secrets.CODE_DEPLOY_DEPLOYMENT_GROUP_NAME }}
          --s3-location bucket=${{ secrets.S3_BUCKET_NAME }},bundleType=zip,key=travel-with-me-build.zip
```

중요한 정보들은 Github Secret으로 감추어두었으니 참고부탁드린다.   

여기서 이제 궁극적으로 해결한 방법을 적어보자면   
`AWS` -> `IAM` -> `사용자` -> `내가 만든 사용자` -> `권한`   
까지 이동을하면 아래와 같은 권한 정책을 설정할 수 있는 화면이 나온다.  
**_(당연하게도 IAM 사용자를 생성하는것이 선행되어야합니다.)_**

![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/77fc1dfb-857e-4bad-a73a-0b74825fc167){: .align-left style="max-width: 100%"}

현재 5개의 권한이 보이지만 원래는 맨아래에 있는   
`CreateDeployMent`라는 정책은 없었다.   

2개는 S3에 대한 권한 설정, 2개는 Code Deploy에 해당하는 권한정책 설정이다.   
여기서 에러가 발생해 `인라인 정책 생성`을 통해서 `CreateDeployMent`라는 정책을 만들었다.


<br/>

`인라인 정책 생성` 버튼을 누르면 아래와 같은 화면으로 넘어온다.   
(JSON 탭을 누르시면 같은 화면으로 넘어옵니다.)

![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/20eff7d9-f775-4bba-9543-27a661904b10){: .align-left style="max-width: 100%"}

해당 정책 편집 페이지에서 위와 같이  

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "codedeploy:*"
            ],
            "Resource": [
                "arn:aws:codedeploy:ap-northeast-2:계정ID:*"
            ]
        }
    ]
}
```

정책을 입력해주면 된다. 여기서 `codedeploy:*` 로 전체권한을 와일드카드로 허용시켜주고   
Resource부분에서도 와일드카드를 사용해 자원에 접근한다는 것을 명시해주었다.    

여기서 환경마다 다를 수 있는 점은   
`ap-northeast-2`부분과 `계정ID` 부분이다.  

만약 서울이면 위와 같이 `ap-northeast-2` 사용하면되고 각자 환경에 맞는 리전을 선택하면된다.   
또한 계정 ID는 본인 계정의 12자리 계정 ID를 뜻한다.    

맨우측 상단에 본인 계정을 눌러보면 탭으로 계정 ID 12자리가   
`xxxx-xxxx-xxxx`와 같은 번호형태로 되어있을 것이다.   
해당 번호를 계정ID자리에 쭉 12자리를 나열하면된다.

<br/>   

해당 정책을 설정하고 다시 Git Actions를 실행시키면  


![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/48ba44fd-97c6-4d6e-8452-5ec9db922ff2){: .align-left style="max-width: 100%"}

정상적으로 에러없이 통과되는 모습을 볼 수 있다.

<br/>
