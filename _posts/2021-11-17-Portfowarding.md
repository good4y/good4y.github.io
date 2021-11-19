포트포워딩 중 문제가 생겼다.
이전 프로젝트에서 80 포트를 5000포트로 포트포워딩 시켰는데  
이번에는 같은 포트를 8000포트로 포트포워딩 해야 했다.  
여기서 이전 설정과 겹쳤는지 계속 이전 프로젝트만 불러오고 8080포트는 계속 포트포워딩이 안됐었다.  
따라서 이전의 설정을 삭제할 필요가 생겼는데, 리눅스 명령어에 익숙하지 않아 한참 고생했다.  
이번 기회에 포트포워딩에 대해 정리하고자 한다.  

## Port
포트포워딩에 대해 정리하기 전에, 포트에 대한 개념부터 알아야 한다.  
포트란 컴퓨터의 IP 뒤에 따라오는 번호로써, IP주소가 192.000.0.000이라고 할 떄  
192.000.0.000:80 이라고 하면 80번이 포트 번호이다.  

내가 만약 1개의 서비스만 제공한다면 192.000.0.000이라는 IP주소만 있어도 사람들이 내 서비스를 이용 할 수 있겠지만  
여러개의 서비스를 제공해야 한다면 그 때 필요한 것이 포트 번호인 것이다.  

## PortFowarding 
한 대의 공유기에 여러 대의 컴퓨터가 연결 되어 있을 때, 어떤 컴퓨터에 요청이 들어왔는지 내부 IP 주소를 지정해주어 알려주는 것이다.

만약 포트포워딩이 되어있지 않다면, 누군가가 내 PC에 80번 포트를 열어주기를 요청했다고 가정해보자.  
우리집에는 PC가 두 대 있고, 스마트폰도 있으며 그 밖에 80번 포트를 열어 줄 수 있는 여러가지 기기가 있다.  
따라서 공유기는 어느 기기의 80번 포트인지 찾지 못하고 브라우저에 오류를 반환할 것이다.  
<img src="https://user-images.githubusercontent.com/43599437/142602740-d42bb076-5c26-4b17-b2d6-a09da887b369.png"/>  
이것을 방지하기 위해, Port 1000으로 접속 시 내 PC의 80번 포트로 접속하라는 설정을 해 둔다면  
공유기 외부에서도 내 PC로 원활히 접속 할 수 있게 된다.  

포트 번호는 세 구간으로 나누어져 있다.
- 0 ~ 1023 사이의 Well-known 포트는 ICANN에 의해 국제적으로 관리되는 포트번호이다.  
- 1024 ~ 49151 사이의 Registered 포트는 중복 방지를 위해 등록 되어있고 통제받지는 않는 포트번호이다.  
- 49152 ~ 65535 사이의 Dynamic 포트는 어떤 프로세스에 의해서도 사용이 가능한 임시 포트이다.  

## iptables
리눅스에서는 보안상의 이유로 1023번 이하의 Well Known포트는 Root 유저만 사용이 가능하다.  
따라서 WEB서비스 요청시 80번 포트를 Binding 하기 위해서는 Root 계정으로 접근하거나 sudo 명령어를 통해 해야한다.  
이를 피하기 위해 포트포워딩을 이용하고

iptables는 리눅스에서 방화벽을 구성하거나 NAT(Network Address Translation)에 사용한다

## 자주 사용하는 iptables command  
- 적용상태 확인  
$ iptables -nvL --line-numbers  
- 특정 IP 차단  
$ iptables -I INPUT -s xxx.xxx.xxx.xxx -j DROP  
- 특정 IP 허용  
$ iptables -I INPUT -s xxx.xxx.xxx.xxx -j ACCEPT  
- 특정 Port 차단  
$ iptables -A INPUT -p tcp --dport xxxx -j DROP  
- 특정 IP / Port 허용  
$ iptables -A INPUT -p tcp -s xxx.xxx.xxx.xxx --dport xxxx -j ACCEPT  
- INPUT chain 에 state 모듈 상태가 New 이고 프로토콜이 tcp 이고 목적지 port 가 8088 인 패킷에 대한 허용추가  
$ iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 8088 -j ACCEPT  
- 규칙삭제  
$ iptables -D INPUT -p tcp -s xxx.xxx.xxx.xxx --dport xxxx -j ACCEPT  
- 적용된 규칙 전부 삭제  
$ iptables --flush  
