---
layout: post
title: Oracle Cloud Free Tier 무료 인스턴스 생성과 접속
subtitle: 
categories: Server
tags: [Server, Oracle]
comments: true
published: true
---

오늘은 [Oracle Cloud 무료 서버]를 생성해보려한다.  

회원 가입과 카드 등록은 되어있다 가정하고  
인스턴스 생성전에 구획생성과 VCN 네트워크 생성  
그리고 인스턴스를 생성하는 과정을 적어보려한다.  
[Oracle 무료 인스턴스 참고 레퍼런스]를 토대로 작업하였습니다.


<br/>

### 📌 구획 생성  

VM 생성 전에 구획을 먼저 생성해야한다.  
구획은 리소스를 포함하고 관리할 수 있는 가상의 공간/영역이다.  

![image](https://user-images.githubusercontent.com/95069395/217751342-152445d0-fe21-4311-80fe-a8a64c90ca13.png){: .align-left style="max-width: 100%"}

<br/>  

### 📌 VCN 네트워크 생성  

![image](https://user-images.githubusercontent.com/95069395/217751354-482bf07f-640e-4ae3-bb9b-5cce4ef7fa7e.png){: .align-left style="max-width: 100%"}

먼저 아까 생성한 구획을 먼저 선택한다음  
VCN 마법사를 시작한다.  

<br/>



![image](https://user-images.githubusercontent.com/95069395/217751372-759eef76-1e3b-4490-a425-b4ee58cb6540.png){: .align-left style="max-width: 100%"}

VCN 마법사 시작 클릭

<br/>

![image](https://user-images.githubusercontent.com/95069395/217751383-d315761d-6825-49df-a01c-ab5f42721432.png){: .align-left style="max-width: 100%"}

VCN 이름을 설정하고 다음 클릭  

<br/>

![image](https://user-images.githubusercontent.com/95069395/217751389-3f48f988-0483-4b5f-9ba7-809822a3f9cb.png){: .align-left style="max-width: 100%"}

생성 버튼을 눌러 리소스 생성을 완료합니다.  

<br/>

### 📌 인스턴스 생성


![image](https://user-images.githubusercontent.com/95069395/217751393-e4c0c83f-a25e-4a21-bfa4-bc896b541579.png){: .align-left style="max-width: 100%"}

VM 인스턴스 생성을 눌러 줍니다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/217751395-bd0a7184-c369-4d92-bd3e-3e289bc4117c.png){: .align-left style="max-width: 100%"}

인스턴스 이름과 이전에 생성한 구획을 선택해줍니다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/217751397-f0633bb2-9f41-4565-b41b-6db79ff0207b.png){: .align-left style="max-width: 100%"}

이미지와 Shape 선택이 가능하고  
항상 무료 적격이라 적혀있는 것을 선택해서 사용해주면된다.  

<br/>


![image](https://user-images.githubusercontent.com/95069395/217751399-50988a2c-4831-40ec-a79f-90f1dbe7d837.png){: .align-left style="max-width: 100%"}

네트워킹도 마찬가지로 이전에 만들어둔 VCN 네트워크를  
선택해주시면 됩니다.  

만약 구획생성과 네트워킹을 만들어주지 않았다면  
인스턴스 생성을하여도 에러가 발생합니다.

<br/>

![image](https://user-images.githubusercontent.com/95069395/217751402-d4de2a99-2591-4217-b226-5514c7f2f972.png){: .align-left style="max-width: 100%"}

SSH 키를 추가해줘야하는데  
이전에 Github를 사용하신분들은 공용키가 이미 있을 것이다.  


```shell
cat ~/.ssh/id_rsa.pub
```

명령어를 입력해 공용 ssh를 붙여넣어주시면 됩니다.   
만약 없다면 생성을 해주시면 되는데, 해당 내용은 스킵하겠습니다.

부트 볼륨과 같은 경우에는 100GB까지 최대로 사용가능하고  
50GB씩 2개의 인스턴스를 만들어 나눠사용할 수도 있습니다.

이제 설정을 끝냈으면, 생성을 눌러 최종완료를 해주시면 됩니다.


<br/>

![image](https://user-images.githubusercontent.com/95069395/217751405-23e9c3ac-d72a-480a-a916-45b2f05ae3e7.png){: .align-left style="max-width: 100%"}

이제 인스턴스 정보에 가서 확인해보시면  
내가만든 인스턴스를 확인해 볼 수 있다.  


<br/>

### 📌 인스턴스 접속하기  

이제 인스턴스를 만들었으니 접속을하는법을 알아보자  
우리는 공용 SSH로 인스턴스를 만들었다.

$HOME/.ssh 경로에 private key가 저장되어 있기 때문에   
암호를 입력하지 않아도 원격 서버에 접속할 수 있다.

```shell
ssh ubuntu@주소
```

만약 private key가 $HOME/.ssh 경로에 위치하지 않은 경우
```shell
ssh -i [private key path] ubuntu@주소
```
를 입력해주면 접속이 가능하다.
\[private key path\]에는 경로를 입력해주면 된다.  
_(나와 같은 경우는 ex)ssh -i /.ssh ubuntu@ip주소)_

터미널에서 입력시

![image](https://user-images.githubusercontent.com/95069395/217751406-423c4acc-c4d0-47d9-bb82-c3ec88b55c10.png){: .align-left style="max-width: 100%"}

와 같이 정상적으로 인스턴스에 접속한 모습을 볼 수 있다 !!

<br/>

FileZilla를 통해서도 접속이 가능하다.   

![image](https://user-images.githubusercontent.com/95069395/217751408-98d77bdb-dcd3-48fd-b019-dc9968f6a565.png){: .align-left style="max-width: 100%"}

SFTP - SSH 프로토콜을 선택해준다음에     
호스트는 인스턴스 주소를 입력해주면 된다.  
그리고 키파일을 공개키가 아닌 `id_rsa` private key를 선택해주면  
정상적으로 접속할 수 있는 모습을 확인해 볼 수 있다.



<br/>

[Oracle 무료 인스턴스 참고 레퍼런스]: https://hoing.io/archives/318  
[Oracle Cloud 무료 서버]:https://www.oracle.com/kr/cloud/free/