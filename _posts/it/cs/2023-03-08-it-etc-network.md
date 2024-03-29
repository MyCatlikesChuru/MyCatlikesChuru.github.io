---
layout: post
title: "네트워크 기초와 TCP/IP 4계층 모델"
subtitle: 면접을 위한 CS 전공지식 노트
categories: CS
tags: [ComputerScience]
comments: true
published: true 
---

### 네트워크 기초

#### 네트워크란?   

노드(node)와 링크(link)가 서로 연결되어 있거나 연결되어 있으며  
리소스를 공유하는 집합을 의미한다. 여기서 노드란 서버,라우터,스위치 등 네트워크   
장치를 의미하고 링크는 유선 또는 무선을 의미한다.   

<br/>

#### 처리량

네트워크를 구축할 때는 '좋은' 네트워크로 만드는 것이 중요하다.   
조은 네트워크란 많은 처리량을 처리할 수 있으며 지연 시간이 짧고 장애 빈도가   
적으며 좋은 보안을 갖춘 네트워크를 말한다.   

처리량(throughput)은 링크 내에서 성공적으로 전달된 데이터의 양을 말하고  
보통 얼만큼의 트래픽을 처리했는지를 나타낸다.   

_**많은 트래픽을 처리한다 = 많은 처리량을 가진다.**_  

![image](https://user-images.githubusercontent.com/95069395/223918518-917aeb8f-f620-401e-94c3-a582ab9869bc.png){: .align-left style="max-width: 70%"}


단위로는 bps(bits per second)를 쓴다. 초당 전송 또는 수신되는 비트수라는 의미이다.   
처리량은 사용자들이 많이 접속할 때마다 커지는 트래픽, 네트워크 장치 간의 대역폭, 네트워크 중간에  
발생하는 에러, 장치의 하드웨어 스펙에 영향을 받는다.   

- 트래픽이 많아졌다 = 흐르는 데이터가 많아졌다.
- 처리량이 많아졌다 = 처리되는 트래픽이 많아졌다.

<br/>

#### 지연 시간  

지연시간이란 요청이 처리되는 시간을 말하며 어떤 메세지가 두 장치 사이를 왕복하는 데 걸리는 시간을 말한다.   

![image](https://user-images.githubusercontent.com/95069395/223918992-2c50b9a0-204f-4bea-9aee-d96fa4720cee.png){: .align-left style="max-width: 70%"}   

해당 그림의 지연시간은 1.7초이다.   
지연 시간은 매체 타입(무선,유선), 패킷 크기, 라우터의 패킷 처리 시간에 영향을 받는다.  

<br/>  

#### 네트워크 토폴로지   

네트워크 토폴로지는 노드와 링크가 어떻게 배치되어 있는지에 대한 방식이자 연결 형태를 의미한다.     
네트워크 토폴로지 방식에는 트리,버스,스타,링형,메시 등이 있다.    

**_❗️링크와 노드란 ?_**  
링크는 네트워크의 연결매체를 얘기하고, 노드는 네트워크 연결 대상이다.(컴퓨터 등)

**1). 트리 토폴로지**

![image](https://user-images.githubusercontent.com/95069395/223926751-9841a69b-abdb-41be-9509-d9bf89e54a60.png){: .align-left style="max-width: 70%"}

장점 : 노드의 추가, 삭제가 쉽다.  
단점 : 트래픽이 몰릴 때, 하위 노드에 영향을 끼칠 수 있다.

**2). 버스 토폴리지**

![image](https://user-images.githubusercontent.com/95069395/223926756-9bcefb65-3304-4f6c-b2ac-7de1db6063bf.png){: .align-left style="max-width: 70%"}

장점 : 신뢰성이 우수하며, 설치 비용이 적다. 중앙 통신 회선에 노드를 추가하거나 삭제하기 쉽다.   
단점 : 스푸핑이 가능하다.   핑

_**❗️스푸핑 ?**_   
LAN 상에서 송신부의 패킷을 관련 없는 수신부의 호스트로 가지 않도록 하는 기능을 못하게 하는 현상.   
올바르게 수신부로 가야할 패킷이 의도치 않은 노드로 가게 된다.

**3). 스타 토폴로지**  

![image](https://user-images.githubusercontent.com/95069395/223926759-9e6ad865-4199-4fee-8f96-69daa6387735.png){: .align-left style="max-width: 70%"}

장점 : 노드를 추가하거나, 에러를 탐지하기가 쉽다. 패킷의 충돌 발생 가능성이 적다.   
어떠한 노드에 장애가 발생했을 때, 이를 발견하기가 쉽다.   
단점 : 중앙 노드에 장애가 발생시, 전체 네트워크에 영향을 준다.

**4). 링형 토폴로지**

![image](https://user-images.githubusercontent.com/95069395/223926760-b057979e-22fe-4900-9dd4-3ceeca729aa8.png){: .align-left style="max-width: 70%"}

장점 : 노드의 수가 늘어나도 네트워크 상의 손실이 거의 없다. 충돌이 발생할 가능성이 적다.
단점 : 한 노드 및 회선에 문제가 발생 시 네트워크 전체에 영향을 미친다. 

**5). 메시 토폴로지**

![image](https://user-images.githubusercontent.com/95069395/223926761-40a387bc-5dbf-46c0-ab45-66a773e2ac35.png){: .align-left style="max-width: 70%"}

장점 : 한 노드에서 장애가 발생해도 경로가 여러개이기 때문에 네트워크 유지가 된다.  
트래픽의 분산처리가 용이하다.   
단점 : 노드의 추가 및 제거가 어렵고, 구축 및 운용 비용이 비싸다.


<br/>

#### 네트워크 분류   

네트워크는 규모를 기반으로 분류할 수 있다.   

![image](https://user-images.githubusercontent.com/95069395/223925633-73f52c7a-9029-4d10-bac5-2eba3951a1c3.png){: .align-left style="max-width: 70%"}  
[그림 출처 - differencebetween.net]  

사무실과 개인적으로 소유 가능한 규모인 LAN (Local Area Network)   
서울시 등 시 정도의 규모인 MAN (Metropolitan Area Network)   
세계 규모의 WAN (Wide Area Network)   

규모에 따라 전송속도의 혼잡도가 증가한다. (LAN < MAN < WAN)  

<br/>

#### 네트워크 성능 분석 명령어   

애플리케이션 코드상에는 전혀 문제가 없는데, 사용자가 서비스로부터 데이터를 가져오지 못하는   
상황이 발생하기도 한다. 이는 네트워크 병목 현상일 가능성이 있고, 네트워크로부터 발생한 문제점   
인지를 확인 후 네트워크 성능 분석을 해봐야 한다. 이때 사용하는 명령어들을 적어보자.   

**1). ping**   

ping(Packet INternet Groper)은 네트워크 상태를 확인하려는 대상 노드를 향해   
일정 크기의 패킷을 전송하는 명령어이다.  

리눅스를 기준으로 구글 IP(66.249.64.97)로 5번의 핑 테스트를 하고 싶다면 

```shell
ping 66.249.64.97 -c 5
PING 66.249.64.97 (66.249.64.97): 56 data bytes
64 bytes from 66.249.64.97: icmp_seq=0 ttl=114 time=238.246 ms
64 bytes from 66.249.64.97: icmp_seq=1 ttl=114 time=246.734 ms
64 bytes from 66.249.64.97: icmp_seq=2 ttl=114 time=237.321 ms
64 bytes from 66.249.64.97: icmp_seq=3 ttl=114 time=236.923 ms
64 bytes from 66.249.64.97: icmp_seq=4 ttl=114 time=238.359 ms
```
위와 같이 테스트 결과가 표시된다.  
해당 노드의 패킷 수신 상태와 도달하기까지 시간등을 알 수 있으며  
네트워크가 잘 연결되어 있는지 확인할 수 있습니다.   

<br/>

**2). netstat**  

접속되어 있는 서비스들의 네트워크 상태를 표시하는 명령어이다.   
네트워크 접속, 라우팅 테이블, 네트워크 프로토콜 등 리스트를 보여줍니다.   

```shell
netstat
Active Internet connections
Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)
tcp4       0      0  192.168.219.106.51923  tp-in-f109.1e100.imaps ESTABLISHED
tcp4      50      0  192.168.219.106.51916  125.209.238.153.imaps  CLOSE_WAIT
tcp4       0      0  192.168.219.106.51915  162.247.243.29.https   ESTABLISHED
tcp4       0      0  192.168.219.106.51914  server-54-230-61.https ESTABLISHED
```

<br/>

**3). nslookup**  

DNS에 관련된 내용을 확인하기 위해 쓰는 명령어이다.   
특정 도메인에 맵핑된 IP를 확인하기 위해 사용한다.

```shell
nslookup
> google.com
Server:		61.41.153.2
Address:	61.41.153.2#53

Non-authoritative answer:
Name:	google.com
Address: 172.217.24.78
```

<br/>

**4). tracert**   

윈도우에서는 tracert이고 리눅스에서는 traceroute라는 명령어이다.  
노드까지 네트워크 경로를 확인할 때 사용하는 명령어이다. 목적지 노드까지 구간들 중   
어느 구간에서 응답 시간이 느려지는지 등을 확인할 수 있다.  

구글 노드까지의 경로를 확인하는 예제를 보자.

```shell
traceroute 172.217.24.78
traceroute to 172.217.24.78 (172.217.24.78), 64 hops max, 52 byte packets
 1  192.168.219.1 (192.168.219.1)  2.039 ms  2.223 ms  2.153 ms
 2  180.230.197.1 (180.230.197.1)  4.158 ms  5.895 ms  5.859 ms
 3  10.240.241.189 (10.240.241.189)  5.620 ms  11.481 ms  5.485 ms
 4  10.204.44.93 (10.204.44.93)  3.383 ms  3.860 ms  3.336 ms
 5  1.213.16.169 (1.213.16.169)  3.285 ms
    61.43.176.9 (61.43.176.9)  2.534 ms
    61.43.207.13 (61.43.207.13)  2.882 ms
 6  * * *
 7  * * *
 8  1.208.167.5 (1.208.167.5)  8.190 ms
    1.213.115.13 (1.213.115.13)  14.300 ms  4.141 ms
 9  61.42.0.26 (61.42.0.26)  41.057 ms
    1.208.167.38 (1.208.167.38)  43.814 ms
    1.208.106.174 (1.208.106.174)  47.441 ms
10  210.107.126.38 (210.107.126.38)  80.176 ms
    1.208.178.26 (1.208.178.26)  63.977 ms
    61.43.220.22 (61.43.220.22)  43.209 ms
11  142.250.168.244 (142.250.168.244)  51.090 ms  58.378 ms  43.391 ms
12  * * *
13  108.170.233.30 (108.170.233.30)  38.043 ms
    142.250.230.198 (142.250.230.198)  42.186 ms
    108.170.241.97 (108.170.241.97)  47.815 ms
14  108.170.241.79 (108.170.241.79)  47.372 ms
    142.251.245.23 (142.251.245.23)  47.688 ms
    108.170.241.80 (108.170.241.80)  47.576 ms
15  sin10s06-in-f14.1e100.net (172.217.24.78)  40.511 ms
    209.85.142.26 (209.85.142.26)  46.256 ms
    sin10s06-in-f14.1e100.net (172.217.24.78)  39.145 ms
```

<br/>

#### 네트워크 프로토콜 표준화   

네트워크 프로토콜이란 다른 장치들끼리 데이터를 주고받기 위해 설정된 공통된 인터페이스를   
말한다. 이러한 프로토콜은 기업이나 개인 발표에서 정하는 것이 아니라 IEEE 또는 IETF라는   
표준화 단체가 이를 정한다.   

IEEE802.3은 유선 LAN 프로토콜로 유선으로 LAN을 구축할 때 쓰이는 프로토콜이다.   
이를 통해 만든 기업이 다른 장치라도 서로 데이터를 수신할 수 있는 것이다.  

<br/>  

### TCP/IP 

#### 계층 구조

**TCP/IP는 데이터가 의도된 목적지에 닿을 수 있도록 보장해주는 프로토콜(통신규약)이다.**

![image](https://user-images.githubusercontent.com/95069395/224016057-b541b0fc-87b3-49a7-b18a-83941944e0fd.png){: .align-left style="max-width: 70%"}  
[그림 출처 - pixelstudio]


<br/>  

OSI 7계층은 네트워크에서 통신이 일어나는 과정을 7단계로 나눈 것을 말한다.   
두 가지 네트워크 모델 중 가장 큰 차이점은 OSI는 개념적 모델로 통신에는 실질적으로   
사용되지 않지만 TCP/IP는 통신에 실질적으로 사용하고있다.   

OSI 7계층은 단계별로 통신을 일어나는 과정을 파악하기 용이하고  
특정 계층에 이상이 생긴다면 해당 계층만 수정해 문제를 해결할 수 있다.

![image](https://user-images.githubusercontent.com/95069395/223934632-1b6ab954-869f-411d-a2a9-878088db0223.png){: .align-left style="max-width: 70%"}

TCP/IP 모델은 OSI 모델에 맵핑이 가능하다.  
TCP/IP 4계층에서 애플리케이션 계층은   
OSI 7계층에서는 3계층을(애플리케이션 계층, 프레젠테이션 계층, 세션 계층)  

마찬가지로 TCP/IP 링크(네트워크) 계층은   
OSI 7계층에서는 2계층을(데이터 링크 계층, 물리 계층) 맵핑할 수 있다.

이 계층들은 특정 계층이 변경되었을 때, 다른 계층이 영향을 받지 않도록 설계되어있다.   
예를들어서 전송 계층에서 TCP를 UDP로 변경했다고 해서 인터넷 웹 브라우저를 다시 설치해야 하는  
것이 아니듯 유연하게 설계되어진 계층들이다.  

<br/>

#### OSI 7계층  

**1). 물리 계층**    

이름 그대로 물리적인 계층이다. 주로 전기적, 기계적, 기능적인 특성을 이용해   
통신 케이블로 데이터를 전송한다. 이 계층에서는 데이터를 전송하려는 데이터가 무엇인지  
에러가 있는지 등에는 전혀 신경 쓰지 않는 계층이다.

해당 계층에 속하는 대표적인 장비는 통신 케이블(랜선), 허브, 리피터가 있다.

![image](https://user-images.githubusercontent.com/95069395/224021103-0fd98a2e-f607-4e36-a510-efd75d44a0fc.png){: .align-left style="max-width: 40%"}

<span style="color:#F04965">**한줄 정리 : 통신 케이블, 리피터, 허브 등을 통해 데이터를 데이터 링크 계층으로 전송한다.**</span>

<br/>

**2). 데이터 링크 계층**

송수신되는 정보의 오류와 흐름을 관리하여 안전한 정보를 전달할 수 있도록 해주는 계층이다.  
이 계층에서는 맥 주소를 가지고 통신하게 된다. 이 계층에 전송되는 단위를 프레임이라하고   
대표적인 장비에는 브리지, 스위치 등이 있다.

![image](https://user-images.githubusercontent.com/95069395/224020739-82a629f2-4132-486f-ae08-331cbd311ea2.png){: .align-left style="max-width: 40%"}

즉, 브리지나 스위치를 통해 맥주소를 가지고 물리계층에서 받은 정보를 네트워크 계층으로 전달한다.  
<span style="color:#F04965">**한줄정리 : 에러검층, 재전송, 흐름제어를 하는 계층**</span>

**_❗️스위치와 허브의 차이_**   
허브가 스위치와 다른점은 연결된 기기 중 하나에서 전송된 패킷이 허브에  
연결된 모든 기기로 브로드캐스팅된다는 점이다. 반면 스위치는 패킷의 목적지 주소로  
지정된 기기로 이어지는 포트로만 패킷이 전달된다.  

**_❗️브로드캐스팅이란?_**  
송신 호스트가 전송한 데이터가 네트워크에 연결된 호스트에 전송되는 방식  
(방송처럼 불특정 다수에게 보내는 형식)

**_❗패킷이란?️_**  
컴퓨터 네트워크가 전달하는 데이터의 형식화된 블록

**_❗MAC 주소?️_**  
컴퓨터나 노트북 등 각 장치에는 네트워크에 연결하기 위한 장치(LAN카드)가 있는데  
이를 구별하기위한 식별번호를 말한다. 6바이트로 구성된다.
️

<br/>

**3). 네트워크 계층**  
이 계층에서는 데이터를 목적지까지 가장 안전하고 빠르게 전달하는 기능(라우팅)이다.  
경로를 선택하고 주소(IP)를 정하고 경로에 따라 패킷을 전달해주는 것이 이계층의 역할이다.  
여기에 사용되는 프로토콜의 종류도 다양하고, 라우팅하는 기술도 다양하다.  

<span style="color:#F04965">**한줄정리 : 주소부여(IP), 경로설정(Route)의 역할을 맡는다**</span>

<br/>

**4). 전송 계층**  
통신을 활성화하기 위한 계층이다. 보통 TCP 프로토콜을 이용하고, 포트를 열어서 응용프로그램들이  
전송을 할 수 있게 한다. 만약 데이터가 왔다면 해당 데이터를 하나로 합쳐서 5계층에 던져준다.   
즉, 전송 계층은 패킷들의 전송이 유효한지 확인하고 전송 실패한 패킷들을 다시 전송한다는 것을 뜻한다.  

TCP 프로토콜?  
- 순서를 보장한다.
- 연결지향 프로토콜을 사용하여 연결한다.
- 가상 패킷 교환 방식을 사용한다.
  - 가상회선 패킷 교환 방식은 각 패킷에는 가상회선 식별자가 포함되며 모든 패킷을 전송하면 가상회선이 해제되고 패킷들은 전송된 `순서대로` 도착하는 방식을 말한다.

UDP 프로토콜?  
- 순서를 보장하지 않는다.
- 수신 여부를 확인하지 않으며 비연결성을 가진다.  
- 데이터그래매 패킷 교환 방식을 사용한다.
  - 패킷이 독립적으로 이동하며 최적의 경로를 선택하여 가는데 하나의 메세지에서 분할된 여러 패킷은 서로 다른 경로로 전송될 수 있으며 도착한 `순서가 다를` 수 있는 방식을 뜻한다.


**_❓순서도 보장되지 않고 비연결을 지향하는데 어디에 쓰는가?_**   
UDP 통신 프로토콜은 신뢰성을 보장하지는 않지만 TCP에 비해 빠른 속도를 제공한다.  
비연결성을 지향하기 때문에 데이터를 재전송할 필요도 없고, 흐름 제어나 에러체크도 항상 필요하진 않다.   
이러한 이유로 신뢰성보다 연속성,성능이 더욱 중요시되는 서비스에서 UDP를 사용한다.   
ex) 멀티미디어 스트리밍, 인터넷 전화 등

<span style="color:#F04965">**한줄정리 : 패킷생성 및 세션계층에 전송**</span>


<br/>

**5). 세션 계층**  
데이터가 통신하기 위한 논리적연결을 말한다. 통신을 하기위한 대문이라 보면된다.   
이 계층에서는 TCP/IP 세션을 만들고 없애는 책임을 가진다.  
즉, 세션 계층은 응용 프로그램 간의 대화를 유지하기 위한 구조를 제공하고, 이를 처리하기 위해  
프로세스들의 논리적인 연결을 담당하는 계층이다.  

<span style="color:#F04965">**한줄정리 : 통신을 하기 위한 세션 확립, 유지, 중단을 수행한다.**</span>

<br/>

**6). 표현 계층**  
이 계층은 코드 간의 번역을 담당하여 사용자 시스템에서 데이터의 형식상 차이를 다르는 부담을  
응용 계층으로부터 덜어준다. MIME 인코딩이나 암호화 등의 동작이 이 계층에서 이루어진다.   

예를 들면 해당 데이터가 TEXT인지, GIF인지, JPG인지의 구분 등이 표현 계층의 몫이다.


<span style="color:#F04965">**한줄정리 : 사용자의 명령어를 완성 및 결과 표현**</span>


<br/>

**7). 애플리케이션(응용) 계층**  
최종 목적지로서 HTTP, FTP, SMTP, POP3, IMAP, Telnet등과 같은 프로토콜이 있다.   
사용자가 네트워크에 엑세스 할 수 있도록 하는 응용 프로그램 그룹이다.  

<span style="color:#F04965">**한줄정리 : 응용 프로세스 간의 정보교환, 파일 전송 등의 서비스를 제공한다.**</span>


<br/>  

#### TCP/IP 4계층

**1). 링크 계층(네트워크 엑세스)**  
OSI 7계층의 물리계층, 데이터 링크 계층에 해당한다.   
데이터를 전기신호로 변환한 , 물리적인 주소인 MAC 주소를 사용해, 알맞는 기기로  
데이터를 전달하는 계층이다.  

<br/>

**2). 인터넷 계층**   
OSI 7계층의 네트워크 계층에 해당한다.  
패킷을 최종 목적지까지 라우팅하는 계층이다.   

<br/>

**3). 전송 계층**    
OSI 7계층의 전송 계층에 해당한다.  
통신 노드 간 신뢰성 있는 데이터 전송을 보장하는 계층이다.   

<br/>

**4). 애플리케이션 계층**  
OSI 7계층의 세션 계층, 표현 계층, 응용 계층에 해당한다.  
사용자와 가장 가까운 계층으로, 사용자-소프트웨어 간 소통을 담당하는 계층이다.  
애플리케이션을 실행하기 위한 데이터 형식이 작성된다.   


<br/>

#### TCP/IP vs OSI 

| 항목  | OSI 7계층     | TCP/IP      |
|-----|-------------|-------------|
| 계층  | 7계층 모델      | 4계층 모델      |
| 구성  | 역할 기반 계층 구성 | 프로토콜의 집합 기반 |
| 기술  | 통신전반 기술 표준화 | 데이터 전송기술 특화 |
| 활용  | 통신 모델 표준 제시 | 실무적 통신기술 구현 |


<br/>

#### TCP 연결 성립 과정

TCP는 신뢰성을 확보할 때 3-way handshake라는 작업을 진행한다.

![image](https://user-images.githubusercontent.com/95069395/224047350-494f0558-eee5-453f-a1bf-0f99fc6e6745.png){: .align-left style="max-width: 70%"}

SYN 단계 : 클라이언트는 서버에 클라이언트의 ISN을 담아 SYN을 보낸다.  
SYN + ACK 단계 : 서버는 클라이언트의 SYN을 수신하고 서버의 ISN을 보내며 승인번호로  
클라이언트의 ISN + 1을 보냅니다.  
ACK 단계 : 클라이언트는 서버의 ISN + 1 한 값인 승인번호를 담아 ACK를 서버에 보낸다.

위의 과정 이후 신뢰성이 구축되고 데이터 전송을 시작한다.  


**_❗️ISN ?_**    
Initial Sequence Numbers의 약어이며  
새로운 TCP연결의 첫 번째 패킷에 할당된 임의의 시퀀스 번호를 말하며 이는 장치마다 다를 수 있다.

<br/>

#### TCP 연결 해제 과정  

TCP가 연결을 해제할 때는 4-way handshake 과정이 발생한다.   

![image](https://user-images.githubusercontent.com/95069395/224054280-9cc960ab-27b1-40a2-b348-2e22f3f6693d.png){: .align-left style="max-width: 70%"}

1번 : 먼저 클라이언트가 연결을 닫으려고 할 때 FIN으로 설정된 세그먼트를 보낸다.   
그리고 클라이언트는 FIN_WAIT_1 상태로 들어가고 서버의 응답을 기다립니다.

2번 : 서버는 클라이언트로 ACK라는 승인 세그먼트를 보낸다. 그리고 CLOSE_WAIT  
상태에 들어간다. 클라이언트가 세그먼트를 받으면 FIN_WAIT_2 상태에 들어간다.

3번 : 서버는 ACK를 보내고 일정 시간 이후에 클라이언트에 FIN이라는 세그먼트를 보내다.  

4번 : 클라이언트는 TIME_WAIT 상태가 되고 다시 서버로 ACK를 보내서 서버는 CLOSED    
상태가 된다. 이후 클라이언트는 어느 정도의 시간을 대기한 후 연결이 닫히고 클라이언트와  
서버의 모든 자원의 연결이 해제된다.
 
TIME_WAIT을 눈여겨 봐야한다. 4번에서 연결을 바로 닫지 않고 일정 시간 뒤에 닫는 이유는  
지연 패킷이 발생할 경우를 대비하기 위함이고, 패킷이 뒤늦게 도달하고 이를 처리하지 못한다면  
데이터 무결성 문제가 발생하기때문이다. 마지막으로 두 장치가 연결이 닫혔는지 확인하기 위해서이다.   
LAST_ACK 상태에서 닫히게되면 다시 새로운 연결을 하려고 할때 장치는 줄곧 LAST_ACK로 되어있기 떄문에   
접속 오류가 나타날 수 있기 때문에 TIME_WAIT라는 일정 시간이 필요하다고한다.

<br/>  

#### 계층 간 데이터 송수신 과정  

HTTP를 통해 웹서버에 있는 데이터를 요청할 경우 아래와 같은 과정이 발생한다.  

![image](https://user-images.githubusercontent.com/95069395/224226896-ae323226-5c8d-4366-8039-ad940fc94f70.png){: .align-left style="max-width: 70%"}

보내는 요청들은 캡슐화 과정을 거쳐 전달되고, 다시 링크 계층을 통해 해당 서버와 통신을하고  
해당 서버의 링크 계층으로부터 애플리케이션까지 비캡슐화 과정을 거쳐 데이터가 전송된다.  
최종적으로 사용자에게는 PDU인 메세지로 전달됩니다.

1). 캡슐화 과정  
상위 계층의 헤더와 데이터를 하위 계층의 데이터 부분에 포함시키고 해당 계층의  
헤더를 삽입하는 과정을 말한다. 

![image](https://user-images.githubusercontent.com/95069395/224228665-0c3da47a-0928-4479-84ac-308166a69be7.png){: .align-left style="max-width: 70%"}

2). 비캡슐화 과정  
하위 계층에서 상위 계층으로 가며 각 계층의 헤더 부분을 제거하는 과정을 말한다.  

![image](https://user-images.githubusercontent.com/95069395/224228697-89a69997-c7c5-4c0d-857b-027ae6b42b8d.png){: .align-left style="max-width: 70%"}

<br/>  

#### PDU  

네트워크의 어떠한 계층에서 계층으로 데이터가 전달될 때 한 덩어리의 단위를   
PDU(Protocol Data Unit)라고 한다. 제어 관련 정보들이 포함된 `헤더`  
데이터를 의미하는 `페이로드`로 구성되어 있으며, 계층마다 부르는 명칭이 다르다.   

- 애플리케이션 계층 : 메세지
- 전송 계층 : 세그먼트(TCP), 데이터그램(UDP)
- 인터넷 계층 : 패킷
- 링크 계층 : 프레임(데이터 링크 계층), 비트(물리 계층)  





<br/>  
<br/>  

✨ 참고 사이트  
- [javatpoint.com]
- [devowen님]
- [wooono님]


<br/>

[그림 출처 - differencebetween.net]:http://www.differencebetween.net/technology/difference-between-lan-wan-and-man/  
[그림 출처 - pixelstudio]:https://velog.io/@pixelstudio/TCPIP%EC%99%80-OSI-7-%EB%A0%88%EC%9D%B4%EC%96%B4%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90
[javatpoint.com]: https://www.javatpoint.com/osi-vs-tcp-ip
[devowen님]:https://devowen.com/344  
[wooono님]: https://wooono.tistory.com/507