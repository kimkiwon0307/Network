# 네트워크 요약

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

# 네트워크 구성 요소

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

# 주소와 통신

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
