---
layout: post
title: Permission denied (publickey) 우분투 에러 조치
subtitle:
categories: Linux/Unix
tags: [Linux, Unix]
comments: true
published: true
---
 
아직 부족한 것도 많고 정확한 지식이 아닐 수도 있지만  
차근차근 정리해보고자 올려본다.

우선 맥북 M1을 사용하고 있고  
EC2에서 우분투를 구매해서 배포해보려고한다.  
EC2 인스턴스를 생성하면  
.pem 파일의 키페어를 받게되고 이걸로 접속을하면된다.

**내 EC2 우분투 접속 명령**

```shell
sudo chmod 400 키파일.pem
ssh -i 키파일.pem ubuntu@내주소
```

키파일.pem : 인스턴스 생성시 등록한 키페어를 올려다 놓으면   
`/Users/ljh/Desktop/~~` 이런식으로 경로가 불러와집니다.  
내주소 : 54.180.104.68과 같은 방식으로 인스턴스의 IPv4 주소를 써주시면 됩니다.

## **본론**

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gO5mxdALoh_qyYSNlGFvrZ3xcP7I0znYaf5bjB_otxkC8N7CS27SUOGBgJdGR92hnc7XdVhTKb7dIYC7vw-lQzc-IWGAA=w1185-h864){: .align-left style="max-width: 100%"}


예전에는 정상적으로 우분투 화면까지 넘어갔는데...  
갑자기 Permission denied (publickey) 에러가 나오면서  
우분투에 접속이 되지않는 현상이 발생했다....

Github ssh key를 만지면서 뭔가 꼬인 것 같아서  
재정비를 해야겠다고 생각을 했다.

아마 통상적인 방법으로는  
PasswordAuthentication를 Yes로 변경해주는   
방법이 가장 많이 구글링될 것이다.

나와 같은 경우는 전부 제대로 설정은되어있고  
무언가 GitHub ssh key를 만들면서 id_rsa가아닌 다른 키로  
인식하고 있는 그런느낌이다...

## **조치 시작**

처음으로 우선 내가가지고 있는 ssh key가 무엇이 있는지 조회해봤다.  

**현재 키 조회하기**

```shell
ls ~/.ssh
```

.ssh 폴더에 접근 명령어

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gO0Dk8ZvLJStChwZFxcd2xsDH9qZn8OyDmaFppSODP8TD69B0ZE4Xu5jSI5Dp_rd-HZ1lmiexZuKiUNT8W3a1pJBgOMTA=w1920-h864){: .align-left style="max-width: 100%"}

.ssh폴더안에는 여러가지 키파일들이 있는 것 같다... 사실 어느게 어떤 역활을  
하는지 명확하게 모르겠지만... 현재 내가 겪고있는 에러는  
Github ssh key로 인증하려고 만들면서 문제가 발생한 것 같다.  
Github Key는 위에 MycatlikesChuru-GitHub와 .pub으로된 파일이다.  
(MycatlikesChuru = 제 깃허브 아이디입니다)

<br/>

터미널로 말고 폴더로 접근을 할 수도 있다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPxmOArwI-J4N2Tet7soYrnZVq_Q_q-_UGdIXAdyD_ULgiCzFnlW0nYFU8UQK_DAurbVUd5PYb8boVhiiCE33w8GacpcQ=w1920-h864){: .align-left style="max-width: 100%"}

Finder에서  
\[Command ⌘ + 방향키 윗키 ▲\]를 누르시면  
상위폴더로 이동하는데

<br/>

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gNBHoeRLdhVU-POH7DSa21ksNTH3ViqamaC11awzIcd0s6oItQnBB0i4u_YuVQwZni_8pzl2IVDosuFyoEL7iDC7A7F0Q=w1920-h864){: .align-left style="max-width: 100%"}

사용자 이름 폴더에서  
\[Command ⌘ + shit + .\]을 누르면 숨김폴더를 볼 수 있다.  
숨김폴더에서 .ssh들어가면 내가 가지고 있는 키의 목록이 보인다.  
이제 가지고 있는 키를 id_rsa키 먼저 삭제해볼 생각이다

<br/>

**id_rsa 삭제**

```shell
rm ~/.ssh/id*
```

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPsZTlkzPfQnnc289BRxHCoiR6CUEy8TZjKKjJFSCVgGSL74Urg2S-woTbE0rmS4HzLgKcd7lJ4vO9e7unTemny314cQg=w1920-h864){: .align-left style="max-width: 100%"}

다시 조회해보면 이렇게 삭제된 모습을 볼 수 있다.  
(폴더에서 삭제해도 무방하다)

<br/>

**키 발급**

```shell
ssh-keygen
```

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gO1OngBZCQL3owrEIqE7NFff7U1n8G94CpQ72rNK8O5rG0_CDcy-K1fcmO6sz_vcpo32yFAap40uBwJRYGMhWSknuEVhw=w1920-h864){: .align-left style="max-width: 100%"}

ssh-keygen을 입력하면 키를 발급받을 수 있다.  
첫번째 질문 Enter file in which to save the key 질문에서는 그냥  
엔터를 누르고 원하는 비밀번호를 2번 입력하면 위와 같이 키가 발급된다.  
다시 조회를해보면 만들어진 것을 확인할 수 있을 것이다.

<br/>

**발급받은키 확인**

```shell
cat ~/.ssh/id_rsa.pub
```

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gOBGGEQANI1GXxRtOnOhymi5qVrX2TFXbVIk3dKnHI5iIKnyvh2XpIBm4TX4gwobkvVClBbCsfTv_HV1FYmmoNuX32ydg=w1920-h864){: .align-left style="max-width: 100%"}

그러면 위 명령어를 입력해서 발급받은키의 내용을  
드래그해서 복사한다음에  
authorized\_keys 파일에 붙여넣어주면된다.  
붙여넣는 방법은 두가지가 있는데 폴더에 들어가서 직접 넣어도되고

<br/>

**키값 authorized_keys에 넣기**

```shell
cat >> ~/.ssh/authorized_keys
```

위의 터미널을 입력후 -> 복사한 키값을 붙여넣고 , 줄바꿈후에 -> Ctrl + D(저장)을 눌러주면 값이 들어간다.  
authorized\_keys파일은 위에 설명한거와같이 .ssh폴더안에 같이 들어있다.  
폴더에 들어가서 값이 제대로 들어가있는걸 확인해보면된다.

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gPHPm3mhCgwqMLHJV3ZA995ilkJz5xPYmy09FSpfS_eD9qbl6eXblDC-cfyNP4Vt1S4JriPBAEn2hHHBt0Sc3a9dkTjYA=w1920-h864){: .align-left style="max-width: 100%"}

이렇게 우선 id_rsa키를 다시 만들고  
키값을 붙여넣어주었고   
이제 MycatlikesChuru-GitHub 파일과 MycatlikesChuru-GitHub.pub 라는 두개의  
파일을 우선 사용하지 않기때문에 삭제를 하였다. (폴더에 접근해서 그냥 Delete시킴)  

이렇게 최종적으로 내가한 작업은

1. id\_rsa키를 다시 발급받고  
2. 발급받은 키를 auhorzied\_keys파일에 값을 붙여넣음  
3. 기존에 Git ssh key 삭제  

이렇게 3개의 과정만 진행한 다음에 다시 우분투에 접속을 시도했다.

```shell
sudo chmod 400 키파일.pem
ssh -i 키파일.pem ubuntu@내주소
```

그랬더니...

![image](https://lh3.googleusercontent.com/u/0/drive-viewer/AFDK6gNhleFsNeV-oZSJ__Zwjk6nT1vDSLBodB3ydIIGNEJUzJk8skhQty4P_-UyTbHWk_kBARWCKhDnFiEaHtCD1wwHv0P2Cg=w1920-h864){: .align-left style="max-width: 100%"}

정말 신기하게도 열린 내서버....!!  
드디어 우분투에 접속을 완료했다....

명확하게 무슨 문제가 일어나서 접속이 안되었는지는 파악이 안되었지만  
Github ssh키로 인증을 받게 되있엇고 권한이 금지되어있었는데.  
삭제를하고 id\_rsa키를 새로 받았더니 작동된걸보면  
우분투 인증이 Github ssh키로 인식해서 셋팅이 제대로 안된 느낌을 받았다.  
후에 ssh key관련되서 어떤식으로 인증이 이루어지고 관리를 해야하는지 공부를 해봐야겠다.

---

#### **번외로!**

혹시나 위와 같은방법으로 했는데 해결이 안된다면  
아래의 내용을 터미널에서 추가해보고 작업해보자!

**하나씩 추가하기**

```shell
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub  
chmod 644 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/known_hosts
```

**한방에 추가하기**

```shell
chmod 700 ~/.ssh & chmod 600 ~/.ssh/id_rsa & chmod 644 ~/.ssh/id_rsa.pub  & chmod 644 ~/.ssh/authorized_keys & chmod 644 ~/.ssh/known_hosts
```

아래 사이트를 참고하였다!  
[참고 - rangyu님의 blog]

[참고 - rangyu님의 blog]: https://github.com/rangyu/TIL/blob/master/ubuntu/SSH-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%A0%91%EC%86%8D-%EC%8B%9C-%ED%8D%BC%EB%AF%B8%EC%85%98-%EB%AC%B8%EC%A0%9C-UNPROTECTED-PRIVATE-KEY-FILE.md

