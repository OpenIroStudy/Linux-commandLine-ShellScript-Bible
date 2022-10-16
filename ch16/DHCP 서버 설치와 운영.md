# DHCP의 개념
DHCP(Dydnamic Host Cofiguration protocol) : 자신의 네트워크 안에 있는 클라이언트 컴퓨터가 부팅될 때 자동으로 IP 주소, 서브넷 마스크, 게이트웨이 주소, DNS 서버 주소를 할당해 주는 것.<br>
=> 관리의 편의성, 이용자의 편의성 ↑, 한정된 IP 주소로 더 많은 IP 주소가 있는 것처럼 활용 가능<br>
<details>
    <summary>공인IP와 사설IP</summary>
    <div markdown="1">
        - 공인 IP는 인터네상에서 공인된 IP. 즉, 전 세계에 1개밖에 없는 IP 주소임.<br>
        - 사설 IP는 내부 네트워크 안에서만 통용되는 IP 주소. 외부 인터넷에서는 인식하지 못함.<br>
        - 원칙적으로는 외부 인터넷에서 사설IP로 접속할 수 없지만 이를 해결하는 방법으로 "마스커레이드" 라고 있다.
    </div>
</details>
<br><br>

# DHCP서버의 작동 원리
1. 클라이언트 PC가 전원을 켬
2. DHCP 서버에 자동으로 IP 주소 요청
3. DHCP 서버가 IP 주소목록에서 할당 전인 IP를 고르고 할당됨으로 변경
4. DHCP 서버가 찾은 IP주소를 할당
5. 클라이언트는 할당된 IP로 인터넷 사용
6. 클라이언트가 전원을 끔
7. IP 주소를 반납
8. 반환된 IP를 DHCP 서버가 IP주소목록에서 할당 전으로 변경

* PC는 아직 DHCP 서버의 주소를 모르는데 어떻게 2 번이 가능할까?
전원이 켜지면 자신의 네트워크케이블에 연결된 모든 컴퓨터에 2번의 주소요청을 Broadcast(전체요청) 함<br>
=> 그중에 DHCP 서버만 응답을 해줌.

DHCP 클라이언트가 되려면?<br>
윈도우 에서는 [Internet Protocol Version4(TCP/IPv4 속성)] 에서 "자동으로 IP 주소 받기"와 "자동으로 DNS 서버 주소 받기"를 선택하면 됨.<br>나는 윈도우11 이라 책이랑 좀 다름.<br>
![01](https://user-images.githubusercontent.com/92290312/196021021-207fba03-1391-467a-96c2-79b021c826ad.png)
<br><br>
리눅스 X윈도에서는 nm-connection-editor 명령을 통해 네트워크 설정을 열고 IPv4 설정을 자동으로 선택<br>
![02](https://user-images.githubusercontent.com/92290312/196021035-921f8f53-a014-47b1-8d0f-d1deca37cf24.png)
<br><br>
리눅스 텍스트 모드에서는 /etc/sysconfig/network-scripts/ifcfg-ens160 파일의 BOOTPROTO 부분을 BOOTPROTO=dhcp 로 수정<br>
![03](https://user-images.githubusercontent.com/92290312/196021042-1bde40dc-5dd4-4cfb-b395-01191d99952a.png)
<br>
<br><br>
# DHCP 구현
- VMware는 기본적으로 DHCP 서버의 기능을 제공하고 있기 때문에 이걸 중지시켜줘야 한다.
1. VMware Workstation Pro 를 실행, 메뉴 중 Edit - Virtual Network Editor 를 선택<br>
만약 창 아래에 'Administrator privileges are reqired~'가쓰여 있으면 그옆에 있는 Change Settings 를 눌러 관리자 권한으로 실행<br>
![04](https://user-images.githubusercontent.com/92290312/196021208-73d0d557-4e20-4b99-bdd8-b7e3b31211b3.png)
<br><br>
2. 기존 DHCP 설정을 확인하기 위해 Vmnet8~ 부분을 선택 후 DHCP Settings를 클릭<br>
그리고 세팅을 기억해 두자.
![05](https://user-images.githubusercontent.com/92290312/196021219-f1f4f29d-f35e-44b4-8133-dafffe1c3e43.png)
<br><br>
3. DHCP Settings 를 나와 NAT Settings 를 눌러 Vmnet8 의 Gateway IP 를 확인한다.<br>
이 게이트웨이가 DNS 서버의 역할도 하기 때문에 DNS 서버도 확인된 것임.<br>
![06](https://user-images.githubusercontent.com/92290312/196021230-150f09d6-0b2e-4dfd-9bd6-0e87b276ff40.png)
<br><br>
4. 이제 Use local DHCP service ~ 의 체크를 풀어 DHCP 기능을 중지하면 된다.<br>
![07](https://user-images.githubusercontent.com/92290312/196021236-12b5f1d1-4310-4cd8-9b54-0f5d1fa854a0.png)
<br><br>
* DHCP 구현
1. DHCP 서버를 설치하자.<br>
`dnf -y install dhcp-server`<br><br>
2. DHCP 서버 설정 파일인 /etc/dhcp/dhcpd.conf를 생성하고 편집<br>
`gedit /etc/dhcp/dhcpd.conf` <br>
![08](https://user-images.githubusercontent.com/92290312/196021253-0dd9d6f9-64a4-4c2f-9d88-01bd79109f2f.png)
<br>

/etc/dhcp/dhcpd.conf 파일 설정 및 설명<br>
|설정|설명|
|:---:|:---:|
| ddns-update-style interim 또는 none | 네임 서버의 동적 업데이트 옵션 |
| subnet 네트워크 주소 netmask 넷마스크{} | DHCP의 네트워크 주소 지정 |
| option routers 게이트웨이IP; | 클라이언트에게 알려줄 게이트웨이 IP 주소 |
| option subnet-mask 서브넷마스크; | 클라이언트에게 알려줄 네트워크의 범위(보통 C클래스 255.255.255.0 임) |
| option domain-name "도메인이름"; | 클라이언트에게 알려줄 도메인 이름 정보 |
| option domain-name-server DNS서버IP; | 클라이언트에게 알려줄 네임 서버의 주소 |
| range dynamic-bootp 시작IP 끝IP; | 클라이언트에게 할당할 IP 주소의 범위 |
| default-lease-time 임대시간(초); | 클라이언트에게 IP주소를 임대해줄 기본적인 초 단위 시간 |
| max-lease-time 임대시간(초); | 클라이언트가 하나의 IP주소를 할당받은 후 보유할 수 있는 최대의 초 단위 시간(이 설정은 특정 컴퓨터가 IP ㅜ소를 고정적으로 보유하는 것을 방지) |
| host ns {<br>hardware Ethernet MAC주소;<br>fixed-address 고정IP주소;<br>} | 특정 컴퓨터(랜카드)에게 고정적인 IP 주소를 할당 할 때 사용 |

이외의 내용은 man dhcpd.conf 명령을 사용해 확인하자.
<br><br>

3. 클라이언트가 IP 주소를 대여해 간 정보를 기록한 파일은 /var/lib/dhcpd/dhcpd.leases 이다. 만약 파일이 없다면 touch 명령을 빈파일을 생성해야함.<br>
`[root@localhost ~]# ls -l /var/lib/dhcpd/dhcpd.leases`<br>
`-rw-r--r-- 1 dhcpd dhcpd 0  5월 11  2019 /var/lib/dhcpd/dhcpd.leases`
<br><br>
4. systemctl restart/enable/status dhcpd 명령을 차례대로 입력해 DHCP 서비스를 시작/확인/상시 가동하자.<br>
만약 서비스 가동을 실패한다면 dhcpd.conf 파일 설정에 이상이 있는 경우가 대부분이므로 다시 확인해보자.<br>
    ```cli
    [root@localhost ~]# systemctl restart dhcpd
    [root@localhost ~]# systemctl enable dhcpd
    [root@localhost ~]# systemctl status dhcpd
    ● dhcpd.service - DHCPv4 Server Daemon
    Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; vendor preset: disa>
    Active: active (running) since Sun 2022-10-16 15:05:41 KST; 48s ago
         Docs: man:dhcpd(8)
            man:dhcpd.conf(5)
    Main PID: 3337 (dhcpd)
   Status: "Dispatching packets..."
    Tasks: 1 (limit: 11376)
   Memory: 10.2M
   CGroup: /system.slice/dhcpd.service
           └─3337 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd>
    ```
