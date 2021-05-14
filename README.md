# IoT216-rp_Linux
### 수업 내용 정리
- Lect 1, 2: 라즈베리파이 시작하기
  - sd카드 포맷
  - raspbian(raspberry Pi OS) 다운 → sd카드에 os porting, rasperry pi에 삽입
  - KBD/Mouse/Monitor 라즈베리파이에 연결 및 전원 인가
  - 해상도 - raspi-config 툴이용해서 해상도 설정 높이기(default; 640x480)
  - vnc 활성화 --> virtual network computing
    - raspberry pi configuration에서 vnc, ssh → enable로⇒ 원격제어 가능 상태--> 입출력 장치 해제 가능
  - wifi 사용 가능(고정 IP 미확보 → 고정 IP설정): 콘솔창 ifconfig에서 inet: ip번호 확인하기
    - wifi모양 우클릭 > wireless설정> ipv4주소: 확인한 ip번호 입력, router: gateway(192.168.0.1) ⇒ reboot
    - 윈도우즈에서 ping ip주소 --> 해당 ip 도달 가능여부 확인
  - 입출력 장치 해제, 윈도우즈로, vnc뷰어 설치 →확인한 IP주소입력, username:pi, ps:raspberry →pc에서 원격접속
  - sudo apt-get update/ upgrade ⇒ 한글 입출력 모듈 설치 (ibus), 폰트 설치
  - >> 한글 오류: raspberry pi configuration> localisation> locale > 언어를 영어(en), us, utf-8
  - samba설치 및 설정 ⇒ pc에서 NFS사용 위함 ==> Network File System
    - samba설치: sudo apt-get install samba-common-bin 
    - root권한으로 설정하기 : sudo passwd> 새 암호 설정 → su입력 후 새 암호 입력
    - --> sudo passwd 남발하면 안됨!!
    - pi > Work파일 만들기 → /etc/samba로 이동 → nano smb.conf 입력 후 마지막에(nano ==> 편집기(vim도 있음))
    - 마지막에 아래 내용 입력 후 저장(ctrl+o) 후 나가기(ctrl+x)
    - --> shift + : ==> 맨 아래로, q!입력 시 저장없이 빠져나옴
    - > [pi]   path=/home/pi/Work writeable=Yes create mask=0777 directory mask=0777   public=no
    - systemctl restart smbd (--> smbd의 d는 데몬)
    - 윈도우즈 파일탐색기에서 라즈비안 ip입력→ \\192.168.0.2(앞에 백슬래시 두개 필요)
  - >> 오류: failed to restart smbd.service unit not found 
    - sudo apt-get remove samba 후에 다시 깔기 → sudo apt-get install samba → restart 성공~!


-------------
- 리눅스 명령어
  - cd: change directory, 디렉토리 이동
    - ~: 홈디렉토리로 바로 이동 --> cd ~
    - cd ..: 상위 디렉토리로
    - cd -: 이동 직전의 디렉토리로 이동
  - ls: list segments, 파일과 디렉토리의 모든 정보 제공
    - 옵션
    - -a: all, 숨겨진 파일이나 디렉토리도 보여줌
    - -l: long, (read,write,exec 권한 알 수 o-> 권한 존재를 1로 표현해서 111(7)로도 표현)-> root/소유자/유저
    - -al: 위 2개 함께 쓰기
    - -t: 파일들을 생성된 시간 별로 표시 --> 최신부터
  - pwd: print work directory, 현재 작업 중인 디렉터리 표시
  - cat: concatenate파일 내용 화면에 출력하거나 파일 만드는 명령어
  - sudo: su(super user), 권한이 없는 사용자는 낮은 수준의 권한이 필요한 파일에 액세스, 수정 가능
  - apt-get: 패키지 관리 명령어 도구
    - upgrade, install, remove
  - exit
- 사용자 
  - $: 일반 유저
  - #: root권한
