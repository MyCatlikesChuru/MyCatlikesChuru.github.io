---
layout: post
title: "Git Actions 자동배포 : Build With Gradle 오류 - Cannot locate a Gradle wrapper properties file at"
subtitle: 
categories: AWS
tags: [AWS, Code Deploy]
comments: true
published: true
---


## 배경

Git Actions를 이용한 CI/CD 구성 진행중 초기 셋팅 시 문제 발생  

```text
Error: Error: Cannot locate a Gradle wrapper properties file at '/home/runner/work/TravelWithMe-sever/TravelWithMe-sever/gradle/wrapper/gradle-wrapper.properties'. Specify 'gradle-version' or 'gradle-executable' for projects without Gradle wrapper configured.
```

<br/>

## 원인

프로젝트 파일위치가 달라 `gradle-version`, `gradle-executable`을 찾을 수 없는 문제  

현재 Travel With Me 폴더 구조는 아래와 같다  
![image](https://github.com/MyCatlikesChuru/MyCatlikesChuru.github.io/assets/95069395/03adc93b-2402-49d0-acc3-f459f79ca6a1){: .align-left style="max-width: 100%"}   

travel-with-me라는 폴더안에 build파일들이 존재하기 때문에 발생한 문제이다  

<br/>

## 해결  

- 기존 초기 생성 yml 파일
```yaml
name: Java CI with Gradle

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

permissions:
  contents: read

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
    - name: Build with Gradle
      uses: gradle/gradle-build-action@67421db6bd0bf253fb4bd25b31ebb98943c375e1
      with:
        arguments: build
```

<br/>

- 폴더 위치 추가

```yaml
name: Java CI with Gradle

on:
  push:
    branches: [ "main" ]
    paths:
      - travel-with-me/** # 해당폴더 변경 감지시
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
        
    - name: Build with Gradle
      uses: gradle/gradle-build-action@67421db6bd0bf253fb4bd25b31ebb98943c375e1
      with:
        arguments: build
        build-root-directory: ./travel-with-me #폴더 경로 추가
```

- 기존 초기 생성 yml 파일