# AWS VPC 과금 문제 해결

## 배경

프리티어 요금제에 RDS가 포함되어 있어, AWS RDS를 사용하여 데이터 베이스 서버를 관리할 계획이었다.

데이터베이스 인스턴스를 생성 후 며칠 뒤 과금 정보를 조회해 보니

<figure><img src="../../../.gitbook/assets/스크린샷 2025-04-14 오후 4.09.15.png" alt=""><figcaption></figcaption></figure>

과금될만한 사항은 추가하지 않았음에도 비용이 나가고 있었다.

## 원인 분석

<figure><img src="../../../.gitbook/assets/스크린샷 2025-04-14 오후 4.08.45.png" alt=""><figcaption></figcaption></figure>

청구서를 확인해 보면 EC2에 할당된 IPv4 주소는 프리티어 요금제로 책정하여 요금이 빠져나가지 않고 있으나, $0.005 per In-use public IPv4 address per hour 항목으로 비용이 발생하고 있다.

찾아보니 2024년부터 IPv4의 고갈 문제로 인해 2개 이상의 IPv4 할당에 대해 과금을 시작했다고 한다.

RDS 인스턴스를 생성할 때 MysqlWorkbench에서 접속하기 위해 **퍼블릭 액세스** 항목을 허용했었다. 외부에서 접근을 허용하기 위해 IPv4를 할당받아 과금이 진행된 것이다.

## 해결 방법

### RDS 퍼블릭 액세스 막기

생성한 RDS 인스턴스를 선택하고 수정에 들어가서 퍼블릭 액세스 항목을 불가능으로 바꾼다. 이로써 과금은 피했다!

<figure><img src="../../../.gitbook/assets/스크린샷 2025-04-14 오후 5.35.42.png" alt=""><figcaption></figcaption></figure>

### 퍼블릭 액세스 없이 워크벤치로 RDS 인스턴스 접근하기

퍼블릭 액세스를 비활성화 해도 RDS 인스턴스와 연결된 EC2 인스턴스에 SSH 접속을 통해서 RDS에 접근할 수 있다.

우선 RDS와 EC2를 연결해준다.&#x20;

1. RDS 인스턴스를 선택하고 작업에서 **EC2 연결 설정**에 들어간다.
2. 연결할 EC2 인스턴스를 선택하고 RDS와 EC2를 연결한다.

연결을 마쳤으면 MysqlWorkbench를 실행시켜 새로운 커넥션을 생성한다.

<figure><img src="../../../.gitbook/assets/스크린샷 2025-04-14 오후 5.53.33 (1).png" alt=""><figcaption></figcaption></figure>

1. Connection Method를 Standard TCP/IP over SSH로 설정한다
2. SSH Hostname에 RDS와 연결된 EC2 인스턴스의 퍼블릭 IP주소를 입력한다.
3.  SSH Username에는 SSH에 접속하기 위한 username을 입력하는데, 이는 OS 별로 기본 username이 다르다. 필자는 Amazon Linux AMI로 지정하여 인스턴스를 시작했기 때문에 ec2-user가 default username이다.

    [https://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/managing-users.html](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/managing-users.html)

    위 링크에 접속하여 기본 사용자 이름을 확인해 보자.
4. SSH Password는 설정한 바가 없다면 그냥 둔다.
5. SSH Key File은 EC2 인스턴스 생성할 때 SSH 접속을 위해 발급받은 .pem 키 파일을 선택한다.
6. MySQL Hostname: RDS 인스턴스의 엔드포인트를 입력한다.
7. MySQL  Server Port: RDS 인스턴스의 Port 번호를 입력한다. 별도로 수정하지 않았다면 기본적으로 3306일 것이다.
8. Username: RDS 인스턴스 생성 시 입력한 마스터 사용자 이름을 입력한다.
9. Password: RDS 인스턴스 생성 시 입력한 마스터 암호를 입력한다.
10. Connection successful!!

