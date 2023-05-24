---
layout: post
title: "Git Actions 자동배포 : Build With Gradle 오류 - submodule yaml 설정방법"
subtitle: 
categories: CI/CD
tags: [Git Actions]
comments: true
published: true
---


## 배경

Git Actions를 이용한 CI/CD 구성 진행중 Build With Gradle 파트에서 모든 Test 코드가 Failed 발생하여 빌드가 제대로 완료되지 않는 현상 발생

![스크린샷 2023-05-10 오후 8.30.47.png](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/df8d7f48-6b2e-4f3a-af2b-5d39054182ab)

```yaml
> Task :test

TravelWithMeApplicationTests > contextLoads() FAILED
    java.lang.IllegalStateException at DefaultCacheAwareContextLoaderDelegate.java:98
        Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException at ConstructorResolver.java:800
            Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException at ConstructorResolver.java:800
                Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException at ConstructorResolver.java:800
                    Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException at ConstructorResolver.java:800
                        Caused by: org.springframework.beans.factory.UnsatisfiedDependencyException at ConstructorResolver.java:800
                            Caused by: org.springframework.beans.factory.BeanCreationException at AutowiredAnnotationBeanPostProcessor.java:405
                                Caused by: java.lang.IllegalArgumentException at PropertyPlaceholderHelper.java:180
```

## 원인

submodule을 이용하여 yml파일을 제대로 가져오지 못해 테스트 코드가 통과되지 않은 것으로 추정

## 해결

[https://light-tree.tistory.com/252](https://light-tree.tistory.com/252) 해당 블로그를 참조해 submodule 이용시    
설정하는 checkout 설정과 토큰을 사용하기 위한 코드 추가.

- 기존 초기 생성 yml 파일

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
```

<br/>

- submodules ture추가와 토큰을  추가

```yaml
    - name: Checkout
      uses: actions/checkout@v3
      with:
        token: ${{ secrets.ACTION_TOKEN }}
        submodules: true
```

<br/>

- 수정된 전체 yml 파일

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
```