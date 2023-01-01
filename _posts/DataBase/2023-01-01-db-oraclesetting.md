---
layout: post
title: Oracle 물
subtitle: 
categories: DB
tags: [DB, Oracle]
comments: true
published: true
---

오늘은 [Oracle Cloud 무료 데이터베이스]를 생성해보려한다.  

회원 가입과 카드 등록은 되어있다 가정하고  
인스턴스 생성전에 구획생성과 VCN 네트워크 생성  
그리고 인스턴스를 생성하는 과정을 적어보려한다.  
[Oracle 무료 인스턴스 참고 레퍼런스]를 토대로 작업하였습니다.


<br/>

### 📌 구획 생성  

VM 생성 전에 구획을 먼저 생성해야한다.  
구획은 리소스를 포함하고 관리할 수 있는 가상의 공간/영역이다.  

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gNs_7nfk_gmvLEvuBwFQHxq_WeESvVrrNgHncikIguZT5WSDBFRWJouafXAfIAnVKgSH8daI2o4KyS-YzP_fNz25gBp=w3024-h1728){: .align-left style="max-width: 100%"}

<br/>  

### 📌 VCN 네트워크 생성  

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPQ25gr2990xuN0OLF_Qr90USye42bZ6DbogZyuabsYL-OejRkV1p7C3LfLIa3X8BF5dgwz7eyH5zr20LtMPIO8nRjHjA=w3024-h1728){: .align-left style="max-width: 100%"}

먼저 아까 생성한 구획을 먼저 선택한다음  
VCN 마법사를 시작한다.  

<br/>



![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gN4OZ_5VRp8kmYeue1N02iMuXfeOZln12KRSuhL_PifDDhaNnn04SjU50HgsoewLDs6y8QAapnbFdv-GGZlSNSp9rSA=w3024-h1728){: .align-left style="max-width: 100%"}

VCN 마법사 시작 클릭

<br/>

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPJqkxzAgS17AG9KpQ1OtqlFWGHiBgNCpvx7ydTjS4MjBJ1jb4a12pnVVnO5Th7cckoNEPKhQmajAVdg02USWVmeLsW=w3024-h1728){: .align-left style="max-width: 100%"}

VCN 이름을 설정하고 다음 클릭  

<br/>

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gMZctP8XvWQbWqzGbVm-w77_ba-OS6OrBJb46yB5-zZv1IxFQDJ9z4q80NN8B-4D2O3qMINEhwKwEaA65i_zTR4aDLa=w3024-h1728){: .align-left style="max-width: 100%"}

생성 버튼을 눌러 리소스 생성을 완료합니다.  

<br/>

### 📌 인스턴스 생성


![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gNGOpUG5JQk1Y20qWPgcKom-VzhRcFsYcez9OIcBXBhA1q6MjLkamcxo3FMef5Uhsg1FdpF8fD0x6dydtnJl3GAjIyoIA=w3024-h1728){: .align-left style="max-width: 100%"}

VM 인스턴스 생성을 눌러 줍니다.

<br/>

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gO1m0uwf3jt9p5biT_6GwYRrmuWIoUXdvRUlZ0HbDrbWTYrj1VK4ci1QVvZhNRVNuKISN1FAY2l3rI7Fyap86OMc6E1EA=w3024-h1728){: .align-left style="max-width: 100%"}

인스턴스 이름과 이전에 생성한 구획을 선택해줍니다.

<br/>

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOdIXghpgxv_vlEivea2ZDnBtqZT-NuITqQPLie9l8He0bfkDT-Of9-oZUhU783AMfII_LhhfdlcLjwMNKIYZz1c37o7w=w1524-h1728){: .align-left style="max-width: 100%"}

이미지와 Shape 선택이 가능하고  
항상 무료 적격이라 적혀있는 것을 선택해서 사용해주면된다.  

<br/>


![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPo3-KkV26L9DztTmGJe7RvC3TpnqdtJ_TuMqP8fQwXvxfrpIKJH8pSbmXrPtmaHYtX1oresm5LE13S4bx5tot7rF5qUQ=w3024-h1728){: .align-left style="max-width: 100%"}

네트워킹도 마찬가지로 이전에 만들어둔 VCN 네트워크를  
선택해주시면 됩니다.  

만약 구획생성과 네트워킹을 만들어주지 않았다면  
인스턴스 생성을하여도 에러가 발생합니다.

<br/>

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gMOEnVFm0OsNdlUigDiwml8a2oJrXHpmUdMcf1PLn7PSX0fIHhR77mwY1-pbOOsFqz_Ld4ydtHu7l_TeA0f4AN17rBy2Q=w1524-h1728){: .align-left style="max-width: 100%"}

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

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gNo2CptbsDAon2CtX6-RAqpmRSSTa7z3m9LWRW9JCFCdVbgMoJTOb9hisTz1OHSLY0EkNb9cXhzDeMauPpUOxUqBrtAXQ=w3024-h1728){: .align-left style="max-width: 100%"}

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
ssh -i private key path ubuntu@주소
```
를 입력해주면 접속이 가능하다. 

터미널에서 입력시

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gN0U_vZhmyBCubkA_yUqYCkiETMcUZtOnZLVXoUTz9DjMI_17cNV1t6S7kLe2Ajxg3hGFEed9nmR6d7kK4Ha7nvpTyeAw=w3024-h1728){: .align-left style="max-width: 100%"}

와 같이 정상적으로 인스턴스에 접속한 모습을 볼 수 있다 !!

<br/>

[Oracle 무료 인스턴스 참고 레퍼런스]: https://hoing.io/archives/318  
[Oracle Cloud 무료 데이터베이스]:https://www.oracle.com/kr/cloud/free/