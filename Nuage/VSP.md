# 1.1. Nuage VSP Architecture
## 1.1.1. VSP
Nuage network Virtualized Services Platform (VSP)는 생성과 동시에 컴퓨트 자원 간의 연결을 자동적으로 배포하고 Data center (DC) 네트워크 가상화를 제공하는 Software-Defined Networking (SDN) 솔루션이다.

Nuage VSP는 `프로그램 가능 한` 비지니스 로직과 강력한 business engine을 활용해 Multi-tenant DC들의 엄격한 요구에 맞출만큼 개방적이고 반응성이 좋은 솔루션을 제공한다. 

Nuage VSP는 기존 DC IP Network 위에 배포될 수 있는 소프트웨어 솔루션이다.
    
## 1.1.2. VSD
![VSD](irnages/vsd.png)



Nuage VSD는 `프로그래밍 가능 한` 정책 엔진이면서 `프로그래밍 가능 한` 통계 엔진으로 
IT 관리자로 하여금 사용자 친화적인 측면에서 자원 정책을 수립하고 적용할 수 있는 유연하고 계층적인 network policy framework다.
Virtual Services Directory (VSD)
Directory의 사전적 의미와 VSD에서의 의미
- 관리서버
- 네트워크 정책 엔진 - 네트워크/보안 등 추상화
- 서비스 템플릿 및 분석

## 1.1.3. VSC
![VSC](irnages/vsc.png)

Virtualzed Services Controller (VSC)
- SDN Controller
- VRS, link with ToR switch and Openflow
- VSC, link with PE and MP-BGP(SROS)

## 1.1.4. VRS
![VRS](irnages/vrs.png)

Virtual Routing & Switching (VRS)
- Distrivuted Switch/Router(L2 ~ L4 rule)
- VRS-G (software VxLAN Gateway)
- 7850VSG (Hardware VxLAN Gateway)

## 1.1.5. Neutron Plugin
Neutron service와 플러그인 형태로 연동

VSD / VSC -> SDN Controller

VRS는 Compute NOde에서 동작, 기존의 OVS(Open VSwitch 대체)

## 1.2. Nuage VSP 물리 구성
MGMT Interface 
    - XMPP: Authentication Config Changes(From VSD to VSC)
    - DC-Gateway : 192.168.210.0/24
    - VSC, VSD, DNS: 192.168.74.0/24

Control Interface
    - Openflow: L2/L3 FIBs to VRS from VSC
    - BGP / EVPN VxLAN: VSC, DC간 L2/L3 FIB 정보 전달
    - 100.64.4.0/24 (Interface IP), 100.244.244.X(Loopback)

## 1.3. Nuage VSP HA 구성

### 1.3.1. keepalived
- HAProxy에 VIP 할당
- VRRP VIP 192.168.74.100
- Priority 101/100

### 1.3.2. HAProxy
- 192.168.74.100:443 listening
- VSD의 WebUI(8443) 서비스로 LB
- Service Health Check

### 1.3.3. DNS
- 이중화

### 1.3.4. VSD
- 삼중화 VSD Cluster
- All Active

### 1.3.5. VSC
- 이중화 Active / Active
- VRS(Open VSwitch)가 Active / Standby 형태로 바라봄
- DC Gateway와 각각 BGP Neighbor

## 1.4. VSP IP 정보 및 접속 정보

## 1.5. VSP 동작 방식

## 1.6. Nuage Host Interface 구성

## 2.1. VSD Functional Components

### 2.1.1. RESTful API
- VSD의 Northbound Interface이며 API Call을 통해 타 시스템과 연동. ComputeManagement Systemc(CMS) Plugin
- LGU+는 NSO를 통해 VSD와 RESTfull API 연동

### 2.1.2. Statistics Engine
- VRS, VSC, VM의 성능 정보를 수집 및 제공
- Elasticsearch Cluster와 연동되어 수집 Data 저장

### 2.1.3. User Management DB
- Multi-tenant 환경을 위한 User 게층 정보 저장.
- Enterprise, Group, User 계층 및 User 및 Group 별 역할 구분

### 2.1.4. Policy Management Engine
- Nuage VSD의 Core Function으로 가상 네트워크 환경을 제어하기 위한 Service Building Block을 구성
- Domain, Zone, Subnet, vPort로 구성되는 계층화된 Building Block

### 2.1.5. Domain
- A logical distributed router that enables L2 & L3 communication.

### 2.1.6. Zone
- A set of network endpoints that must adhere to the same security policies.

### 2.1.7. Subnet
- A layer 2 segment that allows communication between VMs

### 2.1.8. Policies
- Security, QoS, Statistics, Access Control.

### 2.1.9. VSD TCA (Threshold Crossing Alerts) / Analytics Engine
- Elasticsearch 내 VRS의 모든 통계 저장 후 TCA의 임계치 설정에 의해 알람을 발생

### 2.1.10. VSD Mediator 
- VSC와 통신하는 Southbound Interface. VSD의 Policy 정보를 VSC에 전달
- Uses XMPP 

VSP 상태 확인