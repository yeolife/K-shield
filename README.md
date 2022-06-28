# SecurityInfrastructure

VMware를 이용하여 가상환경으로 아래와 그림과 같이 인프라를 구성하였습니다.
![image](https://user-images.githubusercontent.com/82012857/176276420-967e3763-8bf3-4ce9-a01e-29d1bcb54b83.png)

인프라의 구성 명세는 다음과 같습니다.
![image](https://user-images.githubusercontent.com/82012857/176276440-aec1d473-8ed2-495e-b50f-616fcb9eadfb.png)

통신망 4개 - 외부 인터넷(192.168.0.0), DMZ(129.168.80.0), 내부망(192.168.50.0), 오피스망(192.168.40.0)

이미지 6개 - 방화벽 , IPS, 개발자 PC, 관리자 PC, 웹서버 , HIDS 관리서버

※ 각 통신은 최소한의 원칙에 따라 구성되어야 하며 , 필요하지 않은 보안 정책은 차단하여 접근 통제를 수행

※ HIDS 관리 서버는 보안 솔루션인 Wazuh 사용 (HIDS 솔루션 의 서버로 활용하며 , Agent 는 개발자 PC 와 관리자 PC 에서 설치)

※ 웹 서버는 외부 구축되는 호스트 PC 의 외의 다른 장비 예 : 휴대폰 , 노트북 등 에서 HTTP 접속이 가능해야 함

※ 웹 서버는 오피스망의 개발자 PC 만 HTTP 포트와 SSH 포트 접속이 가능해야 함 / 개발자 PC 에서 웹 서버 페이지

※ HIDS 관리 서버는 에이전트 정책 관리를 위해 오피스망 단말 전체를 대상으로 필요한 포트만 활성

※ HIDS 관리 서버의 수정과 서비스 관리를 위하여 관리자 PC 에서만 SSH 접속 가능하도록 설정

※ 방화벽 접근은 오직 관리자 PC 에서만 접근 가능하며 , 관련 설정은 초기 설정 외에 모두 관리자 PC 에서만 수행

※ 내부의 있는 모든 망은 외부와의 인터넷이 불가능

#
### 1. Firewall: untangle을 이용하여 4개의 망에서 All deny기반 외부에서의 모든 접근 통제
![image](https://user-images.githubusercontent.com/82012857/176276484-f29f44aa-eb70-4373-9739-3a90e7cf4dac.png)


100001 : 외부에서 80번 포트로 웹 서버에 접근을 위함

100002 : 개발자PC에서 웹 서버에 22,80 포트 접근을 위함

100003 : Wazuh 에이전트(개발자,관리자)에서 HIDS 서버와 1514,1515,154 포트로 통신을 위함

100004 : 관리자PC에서 HIDS서버에 22번 포트로 접근을 위함

100005 : 관리자PC에서 방화벽 관리를 위해 443포트 접근 가능을 위함

100006 : 화이트리스트 기반이므로 All deny를 위함

#
### 2. 모바일에서 포트포워딩 2번을 통해 웹 서버 접속
![image](https://user-images.githubusercontent.com/82012857/176276505-fcc251a4-da20-4eb0-9233-34fff0c80ceb.png)


1) 외부에서의 웹서버 접속을 위해 무선 랜카드를 이용하여, 호스트PC의 wifi와 스마트폰의 wifi 대역대를 같게 한다(쉽게 말해 같은 와이파이 사용)

2) 웹 서버에서 인바운드 규칙을 설정하여 80, 4000 포트를 허용한다.

3) 스마트폰에서 4000번 포트로 호스트PC(NAT)에게 요청하면 방화벽으로 포트 포워딩(포트포워딩1)

4) 방화벽에서 80번 포트로 IPS에서의 수동 라우팅을 거쳐 웹 서버로 포트 포워딩 (포트포워딩2)

5) 그 결과 4000번 포트로 호스트PC의 IP로 접속하였더니 웹 서버로 정상 접속되었다.

#
### 3. 개발자PC의 웹 서버 접속
![image](https://user-images.githubusercontent.com/82012857/176276536-2fb55c89-017d-4e39-b7f5-a58b3d890d82.png)


1) 방화벽 Rule에 개발자 PC의 IP에서 웹 서버의 IP로의 22,80포트를 허용한다.

2) 서버에 방화벽 설정에 인바운드 규칙으로 22, 80포트를 허용한다.

3) Window 환경이기 때문에 OpenSSH를 설치하여 원격 접속이 가능하게끔 한다.

4) 그 결과 putty의 ssh로그인을 할 수 있다.

#
### 4. HIDS 서버에서의 필요포트 활성화(엄청 헤맸음…)
![image](https://user-images.githubusercontent.com/82012857/176276574-210c00a9-44ca-42db-bb42-0635c2a546d5.png)


1) 개발자PC, 관리자PC에서의 Wazuh활성화를 위해 Centos7 환경에 Wazuh-manager, Elasticsearch, Filebeat, Kibana를 설치함.

2) 에이전트 등록을 위해 각각 키를 등록하였고 그 결과 인터넷이 안되는 환경에서도 정상 작동했다.

3) 키 등록을 위한 1515번 포트와 통신을 위한 1514,154번 포트만을 활성화했고 이 또한 정상작동 했다.
​
#
### 5. 관리자PC에서 HIDS서버 SSH접속
![image](https://user-images.githubusercontent.com/82012857/176276583-a5cf2b82-8ea8-4770-8078-c33f1820338a.png)
1) 방화벽 규칙에 관리자PC의 IP를 HIDS서버의 IP에 22번 포트, TCP 프로토콜를 Allow하게 했다.

2) 그 결과 관리자 PC에서만 ssh접속이 가능했다.

![image](https://user-images.githubusercontent.com/82012857/176276598-e913f9b6-b783-40fb-871d-1b80b0134c6d.png)
3) 개발자 PC에서는 오류가 나온다.

#
### 6. 관리자PC에서의 방화벽 접근
![image](https://user-images.githubusercontent.com/82012857/176276614-2c65fe5c-2143-4974-b89e-458ed3bc997a.png)

1) 방화벽 규칙에 관리자PC의 IP를 방화벽 IP의 443포트를 Allow한다.

2) 다른 곳에서 접속 못하게 Allow http,https non-Wan의 Enable을 체크 해제한다.

3) 관리자PC의 IP만 방화벽 관리 페이지의 80,443포트에 접속할 수 있게끔 새롭게 Access Rule을 만들었다.

![image](https://user-images.githubusercontent.com/82012857/176276636-d7fe4c43-2870-4219-b604-8274f14c041d.png)
![image](https://user-images.githubusercontent.com/82012857/176276643-e470cf5d-e641-45d9-ad66-d5baa0c34b91.png)

관리자는 접속이 가능하나 개발자는 접속이 불가능하다

#
### 7. 내부의 있는 모든 망 외부 인터넷 불가능
![image](https://user-images.githubusercontent.com/82012857/176276657-ec312cb7-3bb2-4609-a64b-5c89a22be5aa.png)
![image](https://user-images.githubusercontent.com/82012857/176276665-35f9188b-b443-46d7-a839-696c23d257f3.png)

관리자PC, 개발자PC에서의 네이버와 구글 접속 불가(Ping도 안됨)

![image](https://user-images.githubusercontent.com/82012857/176276676-856fd005-2ef1-40e2-8992-57eb177587b3.png)
![image](https://user-images.githubusercontent.com/82012857/176276687-15d44081-2987-46d3-9fbe-2c92e6020d30.png)

웹 서버와 HIDS 서버에서의 kisec와 파이어폭스 접속 불가(Ping도 안됨)

#
### 고찰1

방화벽에서 웹서버(End point)로 패킷을 보내야 한다면 IPS(suricataNFQueue,MASQUERADE)를 거쳐야 되기 때문에 목적지가 어딘지 알 수 없다(그렇다고 반대의 게이트웨이로 보낼 수도 없는 상황)

그러므로 방화벽에서 수동 라우팅을 설정해 IPS로 패킷을 보내게 한다.

그러면 IPS가 (192.168.10.0)의 대역대에 있는 목적지 중 하나인 웹 서버에 전달을 해준다.

### 고찰2

스마트폰 같은 외부 환경에서 웹 서버에 접속을 한다면 방화벽, IPS를 거쳐서 접속을 해야하기 때문에 쉽지 않다(망이 2번 바뀜)

그러므로 포트 포워딩을 이용한다. 만약 NAT에 특정 포트가 들어오면 1차로 방화벽으로 포트 포워딩을 해준다.

그 후 방화벽에서 따로 2차로 웹 서버로 포트 포워딩을 해주면 비로소 웹 서버에 접속을 할 수 있게 된다.

※ 그 전에 고찰1의 IPS에서 웹 서버로의 수동라우팅이 설정 되어 있어야 한다.

이게 안 되어 있으면 방화벽에서 웹 서버로 포트 포워딩을 해도 목적지를 찾을 수 없기 때문

### 고찰3

Access Rules에서의 well-known-port인 http(80),https(443)포트의 기존의 룰을 Enable하여 모든 접근을 차단하고,

새로운 룰로 관리자 IP만 접근을 허용하게 하여 이것 또한 화이트리스트 기반으로 설정하여 관리자만 방화벽 관리 페이지에 접근할 수 있게 되었다.


