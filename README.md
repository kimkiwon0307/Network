

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
