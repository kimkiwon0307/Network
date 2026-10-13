

<details> <summary><h3> 1장 네트워크 시작 / 기본 개념 </h3></summary>

# 네트워크 기본

## 프로토콜
프로토콜은 통신을 하기 위한 약속이나 규칙입니다.

* **HTTP/HTTPS**: 웹 통신
* **DNS**: 도메인 $\rightarrow$ IP 변환
* **TCP**: 신뢰성 있는 전송
* **UDP**: 단순하고 빠른 전송
* **IP**: 목적지까지 패킷 전달
* **Ethernet**: 같은 네트워크 구간에서 Frame 전달

## OSI 7계층
* **L7 Application**: HTTP, DNS, SSH
* **L6 Presentation**: 암호화, 인코딩 등
* **L5 Session**: 세션 관리 개념
* **L4 Transport**: TCP, UDP, Port
* **L3 Network**: IP, Router
* **L2 Data Link**: MAC, Ethernet, Switch
* **L1 Physical**: Cable, 전기/광 신호

## MAC / IP / PORT
* **MAC**: 현재 네트워크 구간에서 누구에게 Frame을 보낼까?
* **IP**: 최종 목적지는 어디인가?
* **Port**: 그 서버의 어떤 프로그램/서비스인가?

## Encapsulation / Decapsulation
Application Data
       ↓ ↑
TCP Segment
       ↓ ↑
IP Packet
       ↓ ↑
Ethernet Frame
       ↓ ↑
Bit / Signal

---
</details>



<details> <summary><h3> 2장 네트워크 연결과 구성 요소 </h3></summary>


## LAN / WAN
* **LAN**: 집, 회사, 사무실, 데이터센터 내부처럼 비교적 제한된 범위의 네트워크
* **WAN**: 떨어져 있는 네트워크들을 연결하는 개념

## NIC
* 서버나 PC가 네트워크에 연결되는 인터페이스입니다.
* 리눅스에서는 `ip link` 명령어로 확인할 수 있습니다.
* NIC에는 일반적으로 MAC 주소가 존재합니다.

## Hub / Switch / Router
* **Hub**: 받은 신호를 거의 모든 포트로 전달 (현재는 거의 Switch로 대체됨)
* **Switch**: 주로 L2에서 동작하며, MAC Address를 보고 같은 L2 네트워크 안에서 Frame을 전달
* **Router**: L3에서 Destination IP와 Routing Table을 보고, 서로 다른 IP Network 사이에서 Packet을 전달

## Firewall / IPS / Load Balancer
* **Firewall**: 정책상 이 통신을 허용할까?
* **IPS**: 이 통신 안에 공격 패턴이 있는가?
* **Load Balancer**: 어느 서버에 요청을 보낼까?
---
</details>


<details> <summary><h3> 3장 네트워크 통신 방식과 주소 </h3></summary>

## Unicast / Broadcast / Multicast / Anycast
* **Unicast**: 1대 1 통신 (일반적인 웹 접속은 대부분 Unicast)
* **Broadcast**: 1대 같은 Broadcast Domain 전체 (ARP Request가 대표적)
* **Multicast**: 1대 특정 그룹
* **Anycast**: 여러 대상 중 Routing상 적절한 한 곳

## IPv4
* IPv4는 **32-bit** 주소 체계입니다.
* `192.168.10.0/24`: `/24`는 Network Bit가 24개라는 뜻 (**CIDR**)

## Private IP (사설 IP 주소 대역)
* **Class A**: `10.0.0.0/8` (`10.0.0.0` ~ `10.255.255.255`)
* **Class B**: `172.16.0.0/12` (`172.16.0.0` ~ `172.31.255.255`)
* **Class C**: `192.168.0.0/16` (`192.168.0.0` ~ `192.168.255.255`)

## Subnet
* `192.168.10.10/24`와 `192.168.10.20/24`는 같은 Network에 속합니다.
* `192.168.20.10/24`는 다른 Network입니다.

## 같은 네트워크 통신
* **상황**: PC A (`192.168.10.10`)와 PC B (`192.168.10.20`) 통신 시
  1. PC A $\rightarrow$ ARP 요청
  2. PC B MAC 주소 확인
  3. PC B로 전송
* **프레임 정보**: Ethernet Frame의 `Source MAC`은 PC A MAC, `Destination MAC`은 PC B MAC입니다.

## 다른 네트워크 통신
* **상황**: Client (`192.168.10.10/24`) $\rightarrow$ Server (`192.168.20.10/24`), Gateway (`192.168.10.1`)
* Client는 대역이 다르므로 Server의 MAC 주소를 직접 찾지 않습니다.
* 처음 보내는 Frame 정보:
  * **Ethernet Frame**: `Source MAC` = Client MAC, `Destination MAC` = Default Gateway MAC
  * **IP Packet**: `Source IP` = `192.168.10.10`, `Destination IP` = `192.168.20.10`
> 💡 **핵심 요약**: **MAC**은 현재 구간의 *다음 목적지*, **IP**는 *최종 목적지*를 의미합니다.

## ARP (Address Resolution Protocol)
ARP는 **IP Address $\rightarrow$ MAC Address**를 알아내는 기능입니다.
1. "192.168.10.20의 MAC 주소가 누구야?"
2. **ARP Request**: Broadcast 전송
3. **ARP Reply**: "192.168.10.20은 `00:11:22:33:44:55` 입니다."
4. 결과는 **ARP/Neighbor Cache**에 일정 시간 저장됩니다. (리눅스 명령어: `ip neigh`)

## ARP Cache와 Switch MAC Table 차이
* **Host ARP Cache**: `IP` ↔ `MAC` 매핑 정보 저장
* **Switch MAC Table**: `MAC` ↔ `Switch Port` 매핑 정보 저장

## Switch는 무엇을 보고 학습하나?
* Frame이 들어오면 Switch는 **Source MAC** 주소를 보고 학습합니다.

## TCP / UDP
* **TCP**: 연결성(Connection-oriented), 신뢰성 보장, 순서 보장, 재전송 기능, 3-way Handshake
* **UDP**: 비연결성(Connectionless), TCP보다 단순함, TCP처럼 전달 보장/재전송 기능 없음

</details>

<details> <summary><h3> 4장 스위치와 VLAN / STP </h3></summary>

## Swithch의 기본 역할
1. Switch는 주로 L2 장비이다.
2. Ethernet Frame의 Destination MAC 주소를 보고 어느 Port로 보낼지 결정한다.

## MAC Learning
1. Switch는 Source MAC 주소를 보고 학습한다.
2. Frame 수신 → Source MAC 확인 → MAC Address Table에 저장

## Forwarding
1.Destination MAC이 MAC Table에 있다면 해당 Port로 전달한다.
2.이게 Forwarding이다.

## Flooding
1. Destination MAC을 Switch가 모르면?
2. 들어온 Port를 제외한 같은 VLAN의 다른 Port들로 전송한다.
3. 이걸 Flooding이라고 한다.

## Filtering
1. 불필요한 Frame 전달을 막는 것을 Filtering이라고 볼 수 있다.

## Broadcast와 Switch
1. ARP Request는 Broadcast이므로 Switch가 같은 VLAN의 여러 Port로 Flooding한다.
2. Switch는 기본적으로 Broadcast를 Router처럼 차단하지 않는다.

## VLAN
1. VLAN은 Virtual LAN이다.
2. 하나의 물리적 Switch를 논리적으로 여러 개의 네트워크처럼 분리할 수 있다.
3. VLAN을 사용하는 가장 중요한 목적은 Broadcast Domain 분리이다.

## VLAN 간 통신
1. VLAN10 과 VLAN20 은 서로 다른 네트워크라 통신하려면 L3 장비가 필요하다.
2. 이를 Inter-VLAN Routing이라고 한다.

## Access Port
1. Access Port는 보통 하나의 VLAN에 속하는 Port 이다.
2. 주로 PC, Server 같은 Endpoint를 연결한다.

## Trunk Port
1. 여러 VLAN의 Traffic을 한 링크로 전달한다.

## 802.1Q Tag
1. Ethernet Frame에 VLAN Tag를 붙인건다.
2. Trunk Link에서도 어떤 VLAN Traffic인지 구분할 수 있다.

## L2 Loop

## Broadcast Storm
1. Loop로 Broadcast Frame이 계속 복제되면 네트워크가 Broadcast Traffic으로 가득 찰 수 있다.
2. 이걸 Broadcast Storm이라고 한다.
3. Network 대역폭 사용 증가, Switch CPU 과부하, 정상 Traffic 처리 어려움, 서비스 장애 등이 발생할 수 있다.

## MAC Flapping
1. Loop가 발생하면 Switch 입장에서 같은 MAC 주소가 여러 Port에서 반복해서 보일 수 있다.
2. 이걸 MAC Flapping이라고 한다.

## STP
1. Spanning Tree Protocol
2. L2 Loop를 방지하는 것이다.
3. 물리적으로는 여러 링크를 연결하고, 논리적으로 일부 경로를 Block해서 Loop가 생기지 않게 한다.
4. 평상시에는 Loop가 생길 경로 Block
5. 장애 발생 시 대체 경로 활성화

## RSTP / MSTP
1. RSTP : Rapid Spanning Tree Protocal
2. MSTP는 여러 VLAN을 여러 Spanning Tree Instance로 묶어서 관리하는 방식이다.

## 📋 네트워크 기초 Q&A 정리

### 1. Switch는 MAC Address Table을 어떻게 학습하나요?
* **정답:** Switch는 들어온 Ethernet Frame의 **Source MAC 주소**를 보고, 그 MAC 주소가 어느 Port에 연결되어 있는지 MAC Address Table에 학습합니다.
* **핵심:** `MAC Learning = Source MAC`

---

### 2. Switch가 Destination MAC 주소를 모르면 어떻게 하나요?
* **정답:** 들어온 Port를 제외한 **같은 VLAN의 다른 Port들로 Frame을 Flooding**합니다. 이후 목적지 장비가 응답하면 응답 Frame의 Source MAC을 보고 해당 MAC 주소의 위치를 학습합니다.
* **핵심:** `Destination MAC을 모르면 = Flooding`

---

### 3. ARP Request가 들어오면 Switch는 어떻게 동작하나요?
* **정답:** ARP Request는 **Broadcast Frame**이므로 Switch는 들어온 Port를 제외한 **같은 VLAN의 다른 Port들로 Flooding**합니다.
* **핵심:** `ARP Request = Broadcast` / `Broadcast = 같은 VLAN에 Flooding`

---

### 4. ARP Table과 Switch MAC Table의 차이는 무엇인가요?
* **정답:** 
  * **ARP Table:** IP 주소와 MAC 주소의 매핑 정보를 저장합니다. (`IP ↔ MAC`)
  * **Switch MAC Table:** MAC 주소와 Switch Port의 매핑 정보를 저장합니다. (`MAC ↔ Port`)

---

### 5. VLAN을 사용하는 이유는 무엇인가요?
* **정답:** 하나의 물리적인 Switch를 논리적으로 여러 네트워크로 분리하고, **Broadcast Domain을 나누기** 위해 사용합니다. 이를 통해 Broadcast 범위를 줄이고 네트워크 관리와 보안 측면에서 도움을 줍니다.
* **핵심:** `VLAN = Broadcast Domain 분리`

---

### 6. VLAN 10과 VLAN 20은 기본적으로 서로 통신할 수 있나요?
* **정답:** 기본적으로 **직접 통신할 수 없습니다.** 서로 다른 VLAN은 서로 다른 L2 Broadcast Domain이므로 통신하려면 Router나 L3 Switch를 통한 **Inter-VLAN Routing**이 필요합니다.
* **핵심:** `서로 다른 VLAN 간 통신 = L3 Routing 필요`

---

### 7. Access Port와 Trunk Port의 차이는 무엇인가요?
* **정답:**
  * **Access Port:** 보통 하나의 VLAN에 속하며 PC나 Server 같은 End Device를 연결할 때 사용합니다. (`하나의 VLAN`)
  * **Trunk Port:** 여러 VLAN의 Traffic을 하나의 링크로 전달할 때 사용합니다. (`여러 VLAN`)

---

### 8. 802.1Q란 무엇인가요?
* **정답:** 802.1Q는 Trunk 구간에서 Ethernet Frame에 VLAN 정보를 표시하기 위해 사용하는 **VLAN Tagging 표준**입니다. 여러 VLAN Traffic이 하나의 Trunk Link를 지나갈 때 어느 VLAN의 Frame인지 구분할 수 있게 해줍니다.
* **핵심:** `802.1Q = VLAN Tagging`

---

### 9. L2 Loop가 발생하면 어떤 문제가 생기나요?
* **정답:** Broadcast Frame이 반복해서 순환하면서 **Broadcast Storm**이 발생할 수 있습니다. 또한 같은 MAC 주소가 여러 Port에서 반복적으로 학습되는 **MAC Flapping**이 발생하여 결과적으로 네트워크 성능 저하와 서비스 장애를 일으킵니다.
* **핵심:** `L2 Loop → Broadcast Storm → MAC Flapping → 네트워크 장애`

---

### 10. STP를 사용하는 이유는 무엇인가요?
* **정답:** Switch 간에 이중화된 경로가 있을 때 발생할 수 있는 **L2 Loop를 방지**하기 위해 사용합니다. 여러 경로 중 일부 경로를 논리적으로 차단해서 Loop가 없는 구조를 만듭니다.
* **핵심:** `STP = L2 Loop 방지`

---

### 11. STP는 L2 Loop를 어떻게 방지하나요?
* **정답:** 여러 Switch 경로 중 일부 Port를 논리적으로 **Blocking 상태**로 만들어 Frame이 무한히 순환하지 않도록 합니다. 정상 경로에 장애가 발생하면 기존에 차단되어 있던 대체 경로를 사용합니다.

---

### 12. RSTP와 STP의 차이는 무엇인가요?
* **정답:** RSTP(Rapid Spanning Tree Protocol)는 기존 STP보다 네트워크 장애 발생 시 **더 빠르게 수렴(Convergence)**하도록 개선된 방식입니다.
* **핵심:** `STP = L2 Loop 방지` / `RSTP = 더 빠른 STP`

---

### 13. MAC Flapping이란 무엇인가요?
* **정답:** 같은 MAC 주소가 짧은 시간 동안 여러 Switch Port에서 반복적으로 학습되는 현상으로, L2 Loop 등이 발생했을 때 나타납니다.
  * *예시:* `AA:AA:AA → Fa0/1` ⇄ `AA:AA:AA → Fa0/2`
* **핵심:** `MAC Flapping = 같은 MAC이 여러 Port에서 반복 학습`

---

### 14. Broadcast Domain과 Collision Domain의 차이는 무엇인가요?
* **정답:**
  * **Broadcast Domain:** Broadcast Frame이 전달되는 범위입니다. (VLAN으로 분리)
  * **Collision Domain:** Ethernet에서 Frame 충돌이 발생할 수 있는 범위입니다. (Switch 환경에서는 각 Port마다 분리)

---

### 15. 같은 Switch에 연결되어 있어도 VLAN이 다르면 왜 통신이 안 되나요?
* **정답:** 서로 다른 VLAN은 서로 다른 L2 Broadcast Domain이기 때문에, 같은 물리적 Switch에 연결되어 있어도 논리적으로는 완전히 다른 네트워크로 분리되어 있습니다. 통신을 위해서는 Routing이 필수적입니다.

---

## 📌 핵심 암기 요약

* **Switch 학습:** Source MAC
* **Destination MAC 모름:** Flooding
* **ARP Request:** Broadcast (같은 VLAN에 Flooding)
* **ARP Table:** IP ↔ MAC
* **Switch MAC Table:** MAC ↔ Port
* **VLAN:** Broadcast Domain 분리
* **Access Port:** 하나의 VLAN
* **Trunk Port:** 여러 VLAN
* **802.1Q:** VLAN Tagging
* **L2 Loop:** Broadcast Storm → MAC Flapping
* **STP:** L2 Loop 방지
* **RSTP:** 더 빠른 STP
* **서로 다른 VLAN 간 통신:** L3 Routing 필요

</dtails>

<details> <summary><h3> 5장 라우터와 라우팅 </h3></summary>

## Router의 역할
1. Router는 서로 다른 네트워크를 연결하는 L3 장비이다.
2. Pc가 다른 네트워크에 있는 서버로 가려면 Router가 필요하다.

## Routing이란?
1. Routing은 목적지 네트워크까지 어떤 경로로 갈지 결정하는 과정이다.
2. Router는 내부에 Routing Table을 가지고 있다.
3. Router는 목적지 IP와 Routing Table 표를 비교해서 Packet을 어디로 보낼지 판단한다.

## Routing Table
1. Linux에서 ip route로 볼 수 있다.

## Direct Connected Route
1. Router나 서버의 Interface에 직접 연결된 네트워크이다.
2. Direct Connected : 자신의 Interface가 직접 속해 있는 네트워크

## Static Route
1. 관리자가 직접 경로를 설정하는 방식

## Dynamic Routing
1. Router끼리 Routing 정보를 자동으로 교환하는 방식
2. 대표적으로 OSPF, BGP, RIP 등이 있다.

## Default Route
1. Default Route는 더 구체적인 경로가 없을 때 사용하는 마지막 경로

## Default Gateway
1. Host 입장에서 Default Route의 다음 홉 역할을 하는 Router가 Default Gateway이다.

## Longest Prefix Match
1. Router가 가장 구체적인 Prefix를 선택하는게 Longest Prefix Match이다.

## Route 선택 기준
1. 기본적으로 먼저 Longest Prefix Match가 중요하다.

## Routing과 Fowarding 차이
1. Routing : 경로 계산/선택
2. Fowarding : 실제 전달

## 확인문제
1. Router의 역할은 무엇인가요?

정답:
Router는 서로 다른 네트워크를 연결하는 L3 장비입니다.
Destination IP와 Routing Table을 확인해서
Packet을 어느 경로로 보낼지 결정합니다.

핵심:
Router
= Destination IP + Routing Table


2. Routing이란 무엇인가요?

정답:
Routing은 목적지 Network까지 어떤 경로를 통해 갈지 결정하는 과정입니다.

Router는 Routing Table을 참고해서
목적지 IP에 맞는 경로를 선택합니다.

핵심:
Routing
= 경로 선택


3. Routing Table이란 무엇인가요?

정답:
Routing Table은 목적지 Network로 가기 위한 경로 정보가 저장된 표입니다.

대표적으로 다음과 같은 정보가 포함될 수 있습니다.

Destination Network
Next Hop
Interface
Metric

Router나 Server는 Destination IP를 Routing Table과 비교해서
어느 경로로 Packet을 보낼지 결정합니다.


4. Direct Connected Route란 무엇인가요?

정답:
Router나 Server의 Interface가 직접 속해 있는 Network입니다.

예:

ens32
192.168.10.10/24

이면

192.168.10.0/24

Network는 Direct Connected Network입니다.

핵심:
Direct Connected
= Interface에 직접 연결된 Network


5. Static Route란 무엇인가요?

정답:
관리자가 목적지 Network와 Next Hop을 직접 설정하는 Routing 방식입니다.

장점:
구성이 단순하고 경로를 명확하게 제어할 수 있습니다.

단점:
Network가 커지면 관리해야 할 Route가 많아집니다.

핵심:
Static Route
= 관리자가 직접 설정


6. Dynamic Routing이란 무엇인가요?

정답:
Router끼리 Routing 정보를 자동으로 교환하고
Network 변화에 따라 경로를 학습하는 방식입니다.

대표적인 Routing Protocol로
OSPF, BGP, RIP 등이 있습니다.

핵심:
Dynamic Routing
= Router끼리 Routing 정보 자동 교환


7. Static Routing과 Dynamic Routing의 차이는 무엇인가요?

정답:
Static Routing은 관리자가 직접 경로를 설정합니다.

Dynamic Routing은 Router끼리 Routing Protocol을 이용해서
경로 정보를 자동으로 교환합니다.

Static:
수동 설정

Dynamic:
자동 학습


8. Default Route란 무엇인가요?

정답:
Routing Table에 목적지와 일치하는 더 구체적인 경로가 없을 때
사용하는 기본 경로입니다.

IPv4에서는 다음과 같이 표현합니다.

0.0.0.0/0

Linux에서는 다음처럼 볼 수 있습니다.

default via 192.168.111.2

핵심:
Default Route
= 일치하는 경로가 없을 때 사용하는 마지막 경로


9. Default Gateway란 무엇인가요?

정답:
Host가 자신의 Network 밖에 있는 목적지와 통신할 때
Packet을 전달하는 Router의 Interface입니다.

예:

PC
192.168.10.10/24

Gateway
192.168.10.1

Server
192.168.20.10

PC는 Server로 직접 Frame을 보내는 것이 아니라
먼저 Default Gateway로 전달합니다.

핵심:
다른 Network로 갈 때
→ Default Gateway 사용


10. Default Route와 Default Gateway의 차이는 무엇인가요?

정답:
Default Route는 Routing Table에 있는 경로 정보입니다.

Default Gateway는 Host가 다른 Network로 갈 때
Packet을 전달하는 Router의 주소입니다.

예:

default via 192.168.10.1

여기서

default
= Default Route

192.168.10.1
= Default Gateway


11. 다른 Network의 Server로 통신할 때
Destination MAC은 누구의 MAC 주소인가요?

정답:
Default Gateway의 MAC 주소입니다.

하지만 Destination IP는 최종 Server의 IP 주소입니다.

즉,

Destination MAC
= Default Gateway MAC

Destination IP
= 최종 Server IP


12. Router를 통과하면 MAC 주소와 IP 주소는 어떻게 되나요?

정답:
Router를 통과할 때 Ethernet Frame이 새로 만들어지므로
Source MAC과 Destination MAC은 새로운 L2 구간에 맞게 변경됩니다.

NAT가 없다면
Source IP와 Destination IP는 그대로 유지됩니다.

핵심:

Router 통과
→ MAC 변경
→ IP 유지
  (NAT가 없다면)


13. Longest Prefix Match란 무엇인가요?

정답:
Routing Table에서 Destination IP와 일치하는 Route가 여러 개 있을 때
가장 Prefix가 길고 가장 구체적인 Route를 선택하는 방식입니다.

예:

10.0.0.0/8
10.20.0.0/16
10.20.1.0/24

Destination:
10.20.1.50

선택:
10.20.1.0/24

핵심:
Longest Prefix Match
= 가장 구체적인 Route 선택


14. 다음 Routing Table에서 10.20.1.50으로 갈 때
어떤 Route를 선택하나요?

10.0.0.0/8
10.20.0.0/16
10.20.1.0/24
0.0.0.0/0

정답:
10.20.1.0/24를 선택합니다.

Destination IP와 일치하는 Route 중
가장 Prefix가 긴 Route이기 때문입니다.


15. Default Route보다 구체적인 Route가 있으면 어떤 Route를 사용하나요?

정답:
더 구체적인 Route를 사용합니다.

Default Route는
다른 일치하는 경로가 없을 때만 사용합니다.

예:

192.168.10.0/24
0.0.0.0/0

Destination:
192.168.10.20

선택:
192.168.10.0/24


16. Routing과 Forwarding의 차이는 무엇인가요?

정답:
Routing은 목적지까지 어떤 경로로 갈지 결정하는 과정입니다.

Forwarding은 Routing 결과를 이용해서
실제 Packet을 다음 Interface나 Next Hop으로 보내는 과정입니다.

핵심:

Routing
= 길을 결정

Forwarding
= 결정한 길로 실제 전달


17. Router는 Destination MAC을 보고 Routing 경로를 결정하나요?

정답:
아닙니다.

Router는 Destination IP와 Routing Table을 보고
어느 경로로 보낼지 결정합니다.

MAC 주소는 현재 L2 구간에서
다음 홉에게 Frame을 전달할 때 사용합니다.

핵심:

Switch
→ Destination MAC

Router
→ Destination IP


18. Linux에서 Routing Table을 확인하는 명령어는 무엇인가요?

정답:

ip route

또는

ip r

를 사용할 수 있습니다.


19. Linux에서 특정 목적지로 어떤 경로를 사용할지 확인하려면 어떻게 하나요?

정답:

ip route get 목적지_IP

예:

ip route get 8.8.8.8

이 명령어를 사용하면
어느 Gateway, Interface, Source IP를 사용할지 확인할 수 있습니다.


20. 서버가 외부 Network와 통신되지 않을 때
Routing 관점에서 무엇을 확인해야 하나요?

정답:
다음 항목을 확인할 수 있습니다.

1. IP 주소와 Subnet 설정
2. Routing Table
3. Default Gateway
4. 목적지 Network에 대한 Route 존재 여부
5. Gateway까지 통신 가능한지
6. traceroute를 통한 경로 확인

대표 명령어:

ip addr
ip route
ip route get 목적지_IP
ping Gateway
traceroute 목적지_IP


[핵심 암기]

Router
= L3
= Destination IP + Routing Table

Routing
= 경로 결정

Forwarding
= 실제 Packet 전달

Direct Connected
= Interface에 직접 연결된 Network

Static Route
= 관리자가 직접 설정

Dynamic Routing
= Router끼리 Routing 정보 자동 교환

Default Route
= 더 구체적인 경로가 없을 때 사용
= 0.0.0.0/0

Default Gateway
= 다른 Network로 갈 때 사용하는 Router

Longest Prefix Match
= 가장 구체적인 Prefix 선택

다른 Network 통신
Destination MAC = Gateway MAC
Destination IP = 최종 목적지 IP

Router 통과
MAC = 변경
IP = 유지
(NAT가 없다면)

Linux Routing 확인
= ip route

특정 목적지 경로 확인
= ip route get 목적지_IP

</dtails>


<details> <summary><h3> 6장 L4 장비 / 세션 / 상태 기반 통신 </h3></summary>

## L4에서는 무엇을 볼까?
1. L4는 Transport Layer입니다.
2. TCP / UDP
3. Source Port
4. Destination Port
5. Connection State
6. L4 장비는 단순히 IP만 보는 것이 아니라 TCP/UDP와 Port, 연결 상태까지 활용할 수 있다.

## L4 Load Balancer
1. IP, TCP, UDP, Port, Connection 정보를 기준으로 트래픽을 여러 서버에 나눈다.

## L4와 L7 Load Balancer 차이
1. L4는 Transport Layer 정보를 중심으로 본다.
2. L7은 Http와 같은 Application Layer 정보까지 이해할 수 있다.
3. L4 LB : IP, Port, TCP UDP
4. L7 LB : HTTP Host URL Header 등

## Connection / Session State
1. TCP는 연결지향형이기 때문에 장비가 현재 연결 상태를 기억할 수 있다.
2. Firewall이나 LB는 이 연결을 내부 Table에 기억할 수 잇다.
3. State Table, Connection Table, Session Table 같은 이름으로 부를 수 있다.

## Statefull과 Stateless
1. Stateless 장비는 각 Packet을 독립적으로 판단한다.
2. Stateful 장비는 연결 상태를 기억한다.

## Stateful Firewall
1. Stateful Firewall은 TCP Connection 정보를 저장한다.

## SPI
1. Stateful Packet Inspection
2. Packet 하나만 보는 것이 아니라, 이 Packet이 어떤 연결에 속하는지도 함께 본다.
3. SYN : 연결 시작
4. SYN-ACK : 응답
5. ACK : 연결 성립
6. TCP 상태를 추적할 수 있다.

## Load Balancer의 Connection Table
1. 단순 Round Robin만 하는 게 아니라 현재 Connection을 추적할 수 있다.

## 네트워크 세션과 로그인 세션은 다르다.
1. 네트워크 장비의 Seesion/Connection은 Client IP:Port ↔ Server IP:Port 같은 TCP 연결 상태이다.
2. Application 로그인 Session은 사용자 로그인 정보, 장바구니, 인증 상태 같은 애플리케이션 상태이다.

## Load Balancer 환경에서 로그인 세션 문제
1. Redis 같은 Shared Session Store에 세션을 공통 저장하는 방식도 있다.

## 비대칭 경로
1. Stateful 장비에서는 비대칭 경로가 문제가 될 수 있다.
2. 요청/응답 경로가 달라지면 문제 가능

## Session Sync
1. Firewall이나 Load Balancer를 이중화하면 문제가 하나 있다.
2. 장비 간에 Connection / Session State를 동기화 할 수 있다.
3. 이를 Sesssio Sync / State Sync 라고 한다.

## 확인 문제
# 네트워크 6장 - L4 장비 / 세션 / 상태 기반 통신

## 1. 핵심 개념 정리

### L4 계층

L4는 Transport Layer로 TCP, UDP, Port 번호 등을 다룬다.

```text
L4
= TCP / UDP
= Source Port
= Destination Port
= Connection State
```

예시:

```text
192.168.10.10:51000
        ↓
192.168.20.20:443
```

```text
Source IP        = 192.168.10.10
Source Port      = 51000
Destination IP   = 192.168.20.20
Destination Port = 443
Protocol         = TCP
```

---

### L4 Load Balancer

L4 Load Balancer는 주로 IP, TCP/UDP, Port, Connection 정보를 기반으로 트래픽을 분산한다.

```text
Client
   ↓
L4 Load Balancer
   ├── WEB01:443
   └── WEB02:443
```

```text
L4 LB
= IP / TCP·UDP / Port 기반 분산
```

---

### L4와 L7 Load Balancer 차이

```text
L4 Load Balancer
→ IP
→ TCP / UDP
→ Port
→ Connection

L7 Load Balancer
→ HTTP / HTTPS
→ Host
→ URL Path
→ Header
→ Cookie 등
```

예:

```text
/api
→ API Server

/images
→ Image Server
```

L4와 L7을 구분하는 기준은 **어느 계층의 정보를 해석해서 Backend를 선택하느냐**이다.

---

### Stateful / Stateless

#### Stateless

각 Packet을 독립적으로 판단한다.

```text
Packet 1
→ 별도 판단

Packet 2
→ 별도 판단
```

#### Stateful

현재 Connection 상태를 기억한다.

```text
Client:51000
↔
Server:443

State = ESTABLISHED
```

```text
Stateless
= Packet 단위 판단

Stateful
= Connection 상태 기억
```

---

### Stateful Firewall

Stateful Firewall은 기존 Connection 정보를 State Table에 저장한다.

예:

```text
Client:51000
↔
Server:443
ESTABLISHED
```

Server에서 돌아오는 응답 Traffic이 기존 연결의 응답인지 확인할 수 있다.

---

### SPI

SPI는 Stateful Packet Inspection의 약자이다.

Packet 하나만 독립적으로 판단하는 것이 아니라, 해당 Packet이 어떤 Connection에 속하는지까지 확인한다.

```text
SYN
→ 연결 시작

SYN-ACK
→ 응답

ACK
→ 연결 성립
```

---

### Session / Connection Table

Firewall이나 Load Balancer는 현재 연결 정보를 Table에 저장할 수 있다.

예:

```text
192.168.10.10:51000
↔
192.168.20.20:443

ESTABLISHED
```

Load Balancer도 Client의 Connection을 어느 Backend Server에 전달했는지 기억할 수 있다.

---

### Network Session과 Application Session

둘은 서로 다른 개념이다.

```text
Network Session
= IP + Port 기반 TCP Connection 상태

Application Session
= 로그인 상태, 인증 정보, 장바구니 등
```

```text
Network Session
≠
Application Login Session
```

---

### Load Balancer와 로그인 Session 문제

예:

```text
Client
 ↓
Load Balancer
 ├── WEB01
 └── WEB02
```

WEB01의 메모리에만 로그인 Session을 저장하면 다음 요청이 WEB02로 전달됐을 때 로그인 정보가 없을 수 있다.

해결 방법:

```text
Sticky Session
→ 같은 Client를 같은 Backend로 전달

Shared Session Store
→ Redis 등의 외부 저장소에 Session 저장
```

---

### Asymmetric Routing

요청과 응답의 경로가 서로 다른 것을 비대칭 경로라고 한다.

예:

```text
요청
Client → FW1 → Server

응답
Server → FW2 → Client
```

Stateful 장비에서는 FW2가 기존 Connection을 알지 못해 Packet을 비정상 Traffic으로 판단할 가능성이 있다.

```text
Asymmetric Routing
= 요청/응답 경로가 다름

Stateful 장비에서 문제 발생 가능
```

---

### Session Sync

Firewall이나 Load Balancer를 이중화할 경우 장비 간 Connection 상태를 동기화할 수 있다.

```text
FW1
   ↕ Session Sync
FW2
```

Active 장비에 장애가 발생해도 Standby 장비가 기존 Session 정보를 알고 있다면 기존 Connection 유지에 도움이 된다.

---

# 2. 확인문제 및 정답

## 문제 1

L4 계층에서 대표적으로 다루는 정보는 무엇인가?

### 정답

TCP, UDP, Source Port, Destination Port, Connection State 등을 다룬다.

```text
L4
= TCP / UDP / Port / Connection
```

---

## 문제 2

L4 Load Balancer는 어떤 정보를 기반으로 트래픽을 분산하는가?

### 정답

주로 IP, TCP/UDP, Port, Connection 정보를 기반으로 트래픽을 분산한다.

---

## 문제 3

L4 Load Balancer와 L7 Load Balancer의 가장 큰 차이는 무엇인가?

### 정답

트래픽을 어느 계층까지 해석해서 Backend를 선택하느냐의 차이이다.

```text
L4
→ IP / TCP·UDP / Port

L7
→ HTTP / Host / URL / Header 등
```

---

## 문제 4

Stateless 장비란 무엇인가?

### 정답

이전 통신 상태를 기억하지 않고 각 Packet을 독립적으로 판단하는 장비이다.

```text
Stateless
= Packet 단위 판단
```

---

## 문제 5

Stateful 장비란 무엇인가?

### 정답

기존 Connection 상태를 기억하고, 현재 Packet이 기존 연결과 관련된 Packet인지 판단할 수 있는 장비이다.

```text
Stateful
= Connection 상태 기억
```

---

## 문제 6

Stateful Firewall을 사용하는 이유는 무엇인가?

### 정답

기존 Connection 정보를 기억하기 때문에 요청에 대한 정상적인 응답 Traffic을 기존 연결의 일부로 판단할 수 있다.

---

## 문제 7

SPI란 무엇인가?

### 정답

SPI는 Stateful Packet Inspection의 약자로, Packet 하나만 보는 것이 아니라 해당 Packet이 어떤 Connection에 속하는지도 함께 확인하는 방식이다.

---

## 문제 8

State Table 또는 Session Table에는 어떤 정보가 저장되는가?

### 정답

현재 Connection과 관련된 Source/Destination IP, Port, Protocol, Connection State 등의 정보가 저장될 수 있다.

예:

```text
192.168.10.10:51000
↔
192.168.20.20:443

ESTABLISHED
```

---

## 문제 9

Network Session과 Application Login Session은 같은 개념인가?

### 정답

아니다.

```text
Network Session
= TCP/IP Connection 상태

Application Session
= 로그인, 인증, 장바구니 등의 애플리케이션 상태
```

---

## 문제 10

Load Balancer 환경에서 로그인 Session을 각 WEB Server의 메모리에만 저장하면 어떤 문제가 발생할 수 있는가?

### 정답

사용자가 WEB01에서 로그인한 후 다음 요청이 WEB02로 전달되면 WEB02에는 로그인 Session 정보가 없어 로그인이 풀린 것처럼 보일 수 있다.

---

## 문제 11

Load Balancer 환경의 Session 문제를 해결하는 대표적인 방법은 무엇인가?

### 정답

Sticky Session 또는 Shared Session Store를 사용할 수 있다.

```text
Sticky Session
→ 같은 Client를 같은 Server로 전달

Shared Session Store
→ Redis 등에 Session을 공통 저장
```

---

## 문제 12

Asymmetric Routing이란 무엇인가?

### 정답

Client의 요청 Traffic과 Server의 응답 Traffic이 서로 다른 경로를 사용하는 현상이다.

```text
요청 → FW1
응답 → FW2
```

---

## 문제 13

Asymmetric Routing이 Stateful Firewall에서 문제가 될 수 있는 이유는 무엇인가?

### 정답

요청을 처리한 Firewall과 응답을 처리한 Firewall이 다르면 응답을 받은 Firewall이 기존 Connection 정보를 가지고 있지 않을 수 있기 때문이다.

---

## 문제 14

Session Sync란 무엇인가?

### 정답

이중화된 Firewall이나 Load Balancer 사이에서 Connection 또는 Session 상태 정보를 동기화하는 기능이다.

---

## 문제 15

Session Sync를 사용하는 이유는 무엇인가?

### 정답

Active 장비에 장애가 발생해 Standby 장비로 전환될 때 기존 Connection 정보를 유지하거나 연결 단절을 줄이기 위해 사용한다.

---

# 3. 면접형 질문 및 답변

## 면접 질문 1

### Q. Stateful과 Stateless의 차이를 설명해보세요.

### A.

Stateless는 각 Packet을 독립적으로 판단하고 이전 통신 상태를 기억하지 않습니다.

Stateful은 기존 Connection 상태를 State Table에 저장하고, 현재 들어온 Packet이 기존 연결에 속하는 Packet인지 확인할 수 있습니다.

```text
Stateless
= Packet 단위 판단

Stateful
= Connection 상태 기반 판단
```

---

## 면접 질문 2

### Q. Stateful Firewall은 어떻게 동작하나요?

### A.

Stateful Firewall은 Source/Destination IP, Port, Protocol과 TCP Connection 상태 등을 State Table에 저장합니다.

Client가 Server로 정상적인 연결을 생성하면 Server에서 돌아오는 응답 Traffic을 기존 Connection의 응답으로 판단하여 처리할 수 있습니다.

---

## 면접 질문 3

### Q. SPI가 무엇인가요?

### A.

SPI는 Stateful Packet Inspection의 약자입니다.

Packet 하나만 독립적으로 검사하는 것이 아니라 현재 Packet이 어떤 Connection에 속하는지와 TCP 상태 등을 함께 확인하는 방식입니다.

---

## 면접 질문 4

### Q. L4 Load Balancer와 L7 Load Balancer의 차이는 무엇인가요?

### A.

L4 Load Balancer는 IP, TCP/UDP, Port 등의 Transport Layer 정보를 기반으로 트래픽을 분산합니다.

L7 Load Balancer는 HTTP Host, URL Path, Header 같은 Application Layer 정보까지 해석해서 Backend를 선택할 수 있습니다.

```text
L4
= IP / Port / TCP·UDP

L7
= HTTP / Host / URL / Header
```

---

## 면접 질문 5

### Q. Load Balancer는 단순히 Round Robin으로 서버만 선택하나요?

### A.

아닙니다.

Load Balancer는 새로운 Connection을 Backend에 분산할 뿐만 아니라 기존 Connection이 어느 Backend와 연결되어 있는지 Connection Table 등을 통해 추적할 수 있습니다.

---

## 면접 질문 6

### Q. Load Balancer 환경에서 로그인 Session이 풀리는 문제가 발생할 수 있는 이유는 무엇인가요?

### A.

로그인 Session을 특정 WEB Server의 메모리에만 저장한 경우 다음 요청이 다른 WEB Server로 전달되면 해당 Server에는 사용자 Session 정보가 없을 수 있기 때문입니다.

---

## 면접 질문 7

### Q. Load Balancer 환경의 Session 문제는 어떻게 해결할 수 있나요?

### A.

Sticky Session을 사용해서 같은 사용자의 요청을 같은 Backend Server로 보내거나, Redis 같은 Shared Session Store를 사용해 여러 서버가 동일한 Session 정보를 공유하도록 구성할 수 있습니다.

---

## 면접 질문 8

### Q. Network Session과 Application Session의 차이는 무엇인가요?

### A.

Network Session은 IP와 Port를 기반으로 하는 TCP Connection 상태를 의미합니다.

Application Session은 사용자 로그인 상태나 인증 정보, 장바구니 같은 애플리케이션 수준의 상태를 의미합니다.

따라서 두 Session은 서로 다른 개념입니다.

---

## 면접 질문 9

### Q. Asymmetric Routing이란 무엇인가요?

### A.

Client의 요청 Traffic과 Server의 응답 Traffic이 서로 다른 경로를 사용하는 것을 말합니다.

예를 들어 요청은 FW1을 통과했지만 응답은 FW2를 통과하는 경우입니다.

---

## 면접 질문 10

### Q. Asymmetric Routing이 Stateful Firewall에서 문제가 되는 이유는 무엇인가요?

### A.

FW1은 Client가 시작한 Connection 정보를 알고 있지만 FW2는 그 Connection 정보를 모를 수 있습니다.

따라서 FW2가 응답 Packet을 새로운 또는 비정상 Traffic으로 판단해 차단할 가능성이 있습니다.

---

## 면접 질문 11

### Q. Firewall을 이중화하면 기존 TCP Connection은 무조건 유지되나요?

### A.

무조건 유지되는 것은 아닙니다.

Standby 장비가 기존 Connection 상태를 알지 못하면 Active 장비 장애 후 기존 Connection이 끊길 수 있습니다.

따라서 필요한 경우 장비 간 Session 또는 State 정보를 동기화하는 기능을 사용합니다.

---

## 면접 질문 12

### Q. Session Sync란 무엇이며 왜 필요한가요?

### A.

Session Sync는 이중화된 Firewall이나 Load Balancer 사이에서 Connection 상태 정보를 동기화하는 기능입니다.

Active 장비 장애 시 Standby 장비가 기존 Session 정보를 이어받아 Connection 단절을 줄이기 위해 사용합니다.

---

## 면접 질문 13

### Q. TCP Connection을 식별할 때 어떤 정보들을 사용할 수 있나요?

### A.

일반적으로 Source IP, Source Port, Destination IP, Destination Port, Protocol 정보를 이용해 Connection을 식별할 수 있습니다.

이를 흔히 5-Tuple이라고 표현합니다.

```text
Source IP
Source Port
Destination IP
Destination Port
Protocol
```

---

## 면접 질문 14

### Q. L4 Load Balancer가 Connection 정보를 기억해야 하는 이유는 무엇인가요?

### A.

Client의 Connection을 WEB01로 전달했다면 해당 Connection의 후속 Packet들도 동일한 Backend로 전달해야 정상적인 TCP Connection을 유지할 수 있기 때문입니다.

---

## 면접 질문 15

### Q. Stateful 장비 장애 시 기존 Connection에 어떤 문제가 발생할 수 있나요?

### A.

장비가 가지고 있던 Connection State 정보가 사라지면 기존 연결을 새로운 Traffic으로 인식하거나 정상적으로 처리하지 못해 TCP Connection이 끊길 수 있습니다.

이중화 환경에서는 이를 줄이기 위해 State Sync 또는 Session Sync를 사용할 수 있습니다.

---

# 4. 핵심 암기

```text
L4
= TCP / UDP / Port / Connection

L4 Load Balancer
= IP / TCP·UDP / Port 기반 분산

L7 Load Balancer
= HTTP / Host / URL / Header 기반 분산

Stateless
= Packet 단위 판단

Stateful
= Connection 상태 기억

SPI
= Stateful Packet Inspection

State Table
= Connection 정보 저장

Network Session
= TCP/IP Connection 상태

Application Session
= 로그인 / 인증 / 장바구니 상태

Network Session
≠
Application Session

Sticky Session
= 같은 Client를 같은 Backend로 전달

Shared Session Store
= Redis 등에 Session 공통 저장

Asymmetric Routing
= 요청/응답 경로가 다름

Stateful 장비에서
Asymmetric Routing
→ 문제 발생 가능

Session Sync
= 이중화 장비 간 Connection 상태 동기화

5-Tuple
= Source IP
+ Source Port
+ Destination IP
+ Destination Port
+ Protocol
```

# 5. 6장 한 줄 정리

> L4 장비는 TCP/UDP와 Port, Connection 정보를 이용하며, Stateful 장비는 기존 연결 상태를 기억하기 때문에 비대칭 경로와 이중화 시 Session Sync가 중요하다.
</details>
























