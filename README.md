# IoT216-rp_Linux
### 실습 내용
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
  - sudo apt-get update/ upgrade
  - 한글 사용하기
    - 한글 입출력 모듈 설치 (ibus): sudo apt-get install ibus ibus-hangul
    - 폰트 설치: menu(라즈베리모양) > preferences > add/remove software
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
- Lect 2: gcc(GNU Compiler Collection)
  - hello.c 파일 만들기 --> gcc -o hello hello.c
    - 옵션: -o, output, hello.c 컴파일하여 실행파일 생성 
    - ls -al해보면 녹색으로 hello파일 볼 수 있으며 기존 hello.c와 비교해서 x권한, 즉 실행 가능함이 
  - hello실행시키기 --> ./hello 
    - ./는 현재 경로로, 파일 실행시키기 위해서는 해당 파일 경로와 파일명을 함께 작성해야 함
- Lect 3: gcc, GetToken함수 만들기
  - SSH: secure shell(보안 쉘)
    - 원격지 호스트 커멩서 접속 위해 사용되는 인터넷 프로토콜 --> 기본적으로 CLI상에서 작업, 기본 포트 22
    - 기존 유닉스 시스템 쉘에 원격 접속 시 사용되던 텔넷은 암호화 없어 계정정보 탈취 위험
    - ssh키: ssh client(로컬머신,비공개키), ssh server(원격머신, 공개키) ==> 서버 접속 시 비번 대신 키 제출
  - putty: 터미널 에뮬레이터, ssh이용 위해 사용 --> 원격으로 쉘 이용 가능하게 함
  - VNC로 라즈베리파이 내부에 GetToken 함수만들기
    - char ** 
    - FILE*
- Lect 4: getToken, makefile, wiringPi
  - getToken
    - 문자열 str을 deli로 구분하여 idx위치의 문자열 반환
    - >> 오류: 반환할 문자열을 담을 char *를 함수 내에서 malloc으로 생성 후 반환 시 null
    - 지역변수라서 clear됨 → 매개변수에 결과를 담을 문자열 추가
  - makefile 만드는 과정
    - myHeader.h 파일 만들고 함수 프로토타입들 옮기기 → 기존hello.c에서 원형들 삭제 후 #include “myHeader.h” → 인용부호로 된 것은 시스템 제공 외 로컬 디렉토리 안에서 참조o
    - usr폴더 > include디렉토리 > 이 안의 헤더 파일들(시스템헤더파일)을 <>로 include가능 
    - myLib.c파일 생성 후 함수 정의한 것 옮기기+#include → 실행시 gcc -o hello hello.c myLilb.c
    - makefile생성
    - > CC = gcc   CFLAGS = -g   OBJS = hello.o myLib.o   TARGET = hello   hello : hello.o myLib.o   	$(CC) -o $@ $(OBJS)   hello.o : hello.c myHeader.h   myLib.o : myLib.c
    - 저장 후 make명령 → ls -al 하면 .o파일 생성됨(makefile안에 clean레이블이 이런 중간 파일들 없애주는 역할)
    - > clean :   $(RM) $(OBJS) $(TARGET) core   → make clean 
  - make file: 파일관리 유틸리티, 파일 간의 종속관계 파악해서 Makefile(기술파일)에 적힌 대로 컴파일러에 명령
    - --> SHELL 명령이 순차적으로 실행될 수 있게 함
    - 대문자: macro
    - CC: 컴파일러
    - CFLAGS: 옵션
    - 아래줄에서 $(macro명)
    - “label :  “: 각각의 태그?
    - --> $@: current target
    - --> $^: output file구성 위한 목록들
  - wiringPi.h
    - wiringPiSetup()
    - pinMode(wPi번호,OUTPUT)
    - digitalWrite(wPi번호, HIGH/LOW)
    - gcc -o led LEDcontrol.c -lwiringPi  // -l: link옵션
- Lect 5: ADC/DAC(YL-40)모듈(PCF8591T)- 조도센서(R7), 온도센서(R6), 가변 저항(volumn)
  - 사용
    - raspberry pi configuration > i2c: enable로 > reboot
  - wiringPiI2C.h include
  - wiringPii2CSetup(i2c주소);		// wiringPiI2CSetup(0x48);
    - 주소찾기:  i2cdetect -y 1명령- 6050컨트롤러의 i2c주소 (x축은 16진수, y축은 8개(00~70))
    - I2C모듈의 Handle을 반환  // int hndl = wiringPi~~
  - wiringPiI2CWrite(hndl, 채널 번호);
    - 채널 번호: 0-조도센서, 1-온도센서, 2-AIN2(연결 없는 채널), 3-가변 저항 (AIN: Analog INput)
    - AIN2에 다른 아날로그 센서 결과 연결해서 사용
  - float a = wiringPiI2CRead(hndl);
    - write, read 1번 씩 수행해서 ch초기화 → 실제로는 read를 2번이상 해야할때도
  - 실행: gcc -o test test.c -lwiringPi
  - 채널 값 바꾸기: argv[1]에 값 넣어서 실행 가능
    - 메인 함수의 원형: int main(int argc, char *argv[])
    - GUI에서는 super user될 수 없음 --> CLI에서는 가능
    - argc: argument count
    - gcc -o test test.c 1  // argv[1]=1, 실행
    - argv[0]: 실행 파일 명, argv[1]~
  - 실습 2: 조도 센서 값에 따라 일정값(ex. 100)이상이면 led ON
    - led는 다른 GPIO포트에 연결 후, 어제 실습한 내용에서 wPi번호만 대체
    - >> 저항 560옴으로 조도 센서 구성 --> 저항값이 너무 작아 센서 결과 너무 크게 나옴 --> 모두 255로
    - --> 더 큰 저항으로 대체
- Lect 6. softPWM, ultraSonic Sensor
  - softPwm.h
  - softPwmCreate(int pin, int value, int range): value=초기값, range=듀티비(0~100(%))
  - softPwmWrite ==> for(int i=0;i<pwmRange;i++) softPwmWrite(pNo, i);
    - > for루프 너무 빨라서 soft dimming up힘듦 --> 루프 내에서 매번 delay필요 --> 약 delay(20)줌
    - > i2c(Lect 5)에 적용 시, led꺼진 상태에서 또 threshold보다 작은 값이면 다시 켜졌다가 꺼짐(dimming down)
    - --> 현재 조도 값(val)말고 그 이전의 조도값(prev)변수 추가해서 제어
  - 초음파를 이용한 거리 센서
  - 초음파(ultrasonic): 가청주파수를 넘는 주파수
    - 소리의 전달 속도: 340m/s
  - 센서 원리: 모듈에 DC인가, Trig핀을 통해 신호 펄스 인가 시, 센서에서 음파 신호 발사 후 되돌아온 신호를 Echo를 통해 전달 받음
    - 신호 발사 ~ Echo로 돌아오기까지 시간을 통해서 거리 계산
  - 센서: HC-SR04
    - Trig핀을 통해 10us의 펄스 인가하면 센서는 8개의 40kHz 펄스를 발생시킴
    - 측정된 거리에 따라 150us~25ms 의 펄스를 Echo핀을 통해 출력 시킴
    - 측정 거리 계산 Echo핀 출력 시간(us) * 0.17 = 거리(mm)
    - 8개의 40kHz 펄스 발생시킴 --> trig핀으로 신호 인가 후, 실제 센서가 신호 보낼 시간만큼 기다려야 25us*8(200us)만큼 delay
---------------
### 이론
- 컴파일 과정
  - 전처리 단계: #으로 시작하는 지시자 처리(.c)
  - 컴파일 단계: 어셈블리어로 (.i)
  - 어셈블리 단계: 쪼개서 instruction단위로 만들어 모아 재배치 가능한 목적 프로그램 단위로 (.s)
    - 재배치 가능 = 링커를 통해 다른 목적파일들과 결합 가능 의미
    - 바이너리 파일 생성 
    - gcc -o, *.o
  - 링킹 단계: 목적 파일 엮어서 하나의 실행 파일로(.o)
    - 바이너리 파일
  - executable object program
- GCC(GNU C Compiler)
  - 어셈블리 단계와 링킹 단계
  - --help: 
- I2C: Inter Integrated Circuit
  - sda, scl 두 신호선으로 다수의 I2C통신을 지원하는 디바이스와 데이터를 송수신할 수 있는 통신 방식
    - IC들 간의 데이터 통신을 목적으로 하는 통신 방식
  - 하나의 마스터와 다수의 슬레이브로 연결 구성됨 → 마스터에서 기준클럭(scl)을 생성, 클럭에 맞춰 데이터(sda)를 전송 및 수신 ⇒serial clock, serial data
  - 각 송수신은 구분되어있는(동시 불가) 반이중(half-duplex)
  - I2C인터페이스: 라즈베리파이의 GPIO는 디지털 입출력만 가능
    - 아날로그 신호원의 디바이스는 ADC필요
    - 시작조건: scl-high, sda-high→ low / 종료조건: scl-high, sda-low→ high
- PWM(pulse width modultaion)
  - 스위치 on/off위한 신호의 패턴
    - ex) 차량의 인버터, 차량의 모터는 공급받는 전류의 주파수와 크기로 회전 속도와 토크 조절 가능→ 운전하려면 원하는 f와 크기의 교류 전원 필요 ==> 인버터는 스위치 on/off동작으로 베터리에서 받은 직류를 교류로 바궈줌
  - duty Cycle: 신호 1(high)와 0(low)의 비율
- VDD: Drain, VCC: Collector
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
  - 버전 확인하기 ex) gcc -v  // gcc 컴파일러 버전 확인
  - - redirection: 리눅스 스트림의 방향 조정 (stream개념 확실히 잡아야- c/c++에서 stdin, stdout)
    - >: 명령의 결과를 파일로 저장
    - >>: 명령 결과를 파일에 추가
    - <: 파일의 데이터를 명령에 입력
  - find: ex) strcmp함수가 선언된 헤더파일 찾기
    - 헤더파일이 모인 폴더로 이동: cd /usr/include
    - find / *.h | grep -r strcmp
    - 해당 결과에서 extern인 위치 찾기 --> strcmp 선언부
- 사용자 
  - $: 일반 유저
  - #: root권한
------------
### C언어
- #include <conio.h> 유닉스에서x → key event x 
    - 한사람이 키보드를 독점하는 이벤트 사용 불가, 유닉스 멀티 유저 기반? 이므로
    - getch()대신 표준함수인 getchar()써야
- FILE *fp = fopen(“test.txt”,”ab”);
    - 파일 다루는데 익숙해져야
    - FILE *fp  = fopen(“파일명(파일경로)”,”옵션”);
  - 옵션
    - a옵션: append(기존에 있으면 추가, 없으면 생성해서 추가)
    - b옵션: binary형태의 데이터형태(없는 것과 차이는 \n처리만 차이)
  - fscanf, fprintf: scanf, printf 사용법과 동일하나, 파일 포인터 매개변수만 추가하면 됨 
    - fprintf(fp,”%s”,buf); 파일로 내보냄 / fscanf(fp,”%s”,str); 파일 내용 받아옴
  - fclose(fp);  // 반드시 닫아줘야
- c와 c# 차이 
    - c#에서 while 안에는 bool, c는 0외 숫자(true)/ 0(false)도 가능 
    - c#에서는 null → c에서는 NULL
    - c는 함수 이름 같은거 하나만 가능(함수 오버로드x) → EX, EX1, `~~
- <string.h>
    - strlen함수
    - strcpy함수
    - strncpy → strcpy에 복사할 길이(사이즈) 인수 하나 더
    - → 복사한 문자열 마지막에 null(0)처리 안함 → 별도로 해줘야
- ** : 포인터의 포인터 
- 문자열
    - char[] → 괄호 안에 반드시 상수써야
    - char 무조건 1byte, 한글은 2byte, 문자열 끝에 반드시 null → split시 사용
- malloc: memory allocation
  - free
    - 메모리 누수 발생 가능성: free(메모리 개방)
    - 쓰고 안닫으면? segmentation error → 그냥 모든 ㅇㅔ러인가?
  - size
    - default는 1바이트 = 1 char(1byte)
    - char** ar2 = malloc(cnt(str)*sizeof(char*));	// 포인터 변수의 크기(32bit면 4바이트)
  - stack 영역 x→ data영역차지(시스템 종료 후에도 남아 있으니 free해야한다)
    - ⇒ 코드영역과 스택영역은 로컬 데이터 공유
- 사용자 정의 함수에서 만든 지역변수를 리턴 → stack영역(재사용영역)
    - 리턴 시, clear되서 null됨
