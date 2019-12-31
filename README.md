# Linux-terminal
리눅스 터미널에서 사용하는 명령어 관련 팁 정리

## 서비스(Daemon)
### 서비스(Daemon) 시작 / 종료
* 서비스(데몬)으로 프로그램 시작 - $ `service [프로그램 이름] start`
* 서비스(데몬) 프로그램 종료 - $ `service [프로그램 이름] stop` 
### 부팅시 자동 시작되는 서비스
* 실행 스크립트 생성
    - /etc/init.d 디렉토리 밑에 실행 스크립트 생성 
* update-rc.d 로 서비스 등록
    - update-rc.d [-n | -f] <스크립트명> [remove | defaults] <실행순서>
    - 서비스 등록 예시) /etc/init.d 아래 start_myProgram 실행 파일 있고 실행순서(우선순위)를 99로 원한다고 가정
    - $ `update-rc.d start_myProgram defaults 99`
* 서비스 삭제 예시)
    - $ `update-rc.d -f start_myProgram remove`
* 서비스 등록 확인 예시)
    - CLI 서비스는 `/etc/rc3.d`에서 확인, GUI 서비스는 `/etc/rc5.d`에서 확인
    - $ `ls /etc/rc3.d` 에서 `S99start_myProgram` 있는지 확인
* 참조 사이트 : [링크](https://wiki.debianusers.or.kr/index.php?title=Update-rc.d)

### 백그라운드 프로세스
* 현재 포그라운드로 실행중인 프로세스 일시정지 : `ctrl + z` 
* 일시정지한 프로세스 목록 보기 : $ `jobs` 
* 가장 최근 멈춘 프로그램 재시작 : $ `fg`  
* jobs에서 보이는 목록 앞에 숫자에 해당하는 프로그램 시작 : `fg %[숫자]` 
* 숫자에 해당하는 프로세스 종료 : $ `kill %[숫자]` 

### 현재 사용하는 쉘 확인
* $ `echo $0`

### syslog 기록되는 로그 파일 위치
* /var/log/messages

## 서비스 등록
### 서비스 스크립트 생성
* /etc/init.d 디렉토리 밑에 서비스 스크립트 생성 
* 서비스 스크립트 규격
```
case $1 in
 start)
   <실행할 명령이나 스크립트명>
   ;;
 stop)
   <중지할 명령이나 스크립트명>
   ;;
 restart)
   <중지하고 다시 시작할 명령이나 스크립트명>
   ;;
 *)
   echo "Usage: /etc/init.d/$NAME {start|stop|restart}"
   exit 1;
   ;;
esac 

exit 0
```
* 서비스 스크립트 예제 : 실제 실행 파일은 myservice.sh(사용자가 만든 실행 파일)
```
$ cd /etc/init.d
$ vi my_service

## my_service 내용
#!/bin/sh

NAME=myservice.sh
case "$1" in
  start)
    echo -e "Starting My Service: "
    start-stop-daemon -S -b -a /home/jujin/$NAME
    echo -e "my_service started\n"
    ;;
  stop)
    echo -e "Stop My Service: "
    start-stop-daemon -K -n /home/jujin/$NAME
    echo "stopped"
    ;;
  restart)
    $0 stop
    $0 start
    ;;
  *)
    echo "Usage /etc/init.d/$NAME { start | stop | restart }" >&2
    exit 1
    ;;
esac
exit 0
```
### update-rc.d 로 서비스 등록
* update-rc.d [-n | -f] <스크립트명> [remove | defaults] <실행순서>
* 서비스 등록 예시) /etc/init.d 아래 `my_service` 서비스 스크립트 파일 있고 실행 순서를 99로 원한다고 가정
```
# update-rc.d my_service defaults 99
```
* 서비스 삭제 예시)
`# update-rc.d -f my_service remove`
* 참조 사이트 : [링크](https://wiki.debianusers.or.kr/index.php?title=Update-rc.d)

### 주의 사항
* 서비스 실행시에 실행 디렉토리는 루트(/)로 지정됨으로 해당 실행파일과 상대경로로 연결된 파일이 있을 경우 정상 동작하지 않을 수 있다.

## ssh server
### 설치 
* 우분투(Ubuntu) : `apt-get install openssh-server`
### 설정
* 위치 : /etc/ssh
* ssh_host_res_key : 해당 리눅스 접속을 위한 프라이빗 키
* sshd_config 
  * ssh 접근을 위한 config 파일
  * 변경 이후 `$ service ssh restart`로 재시작한다.
* 참고사이트 : [링크](http://programmingskills.net/archives/315)

## 네트워크 관련 명령어
### netcat
* 설치 : $ `yum -y install net-tools`
* 사용법 
    - 1. 외부 서버에 특정 포트로 접속이 가능한지 확인 : $ `nc -z [외부 서버 주소] [포트]`
    - 예시(123.456.789.101 서버 mysql(3306포트) 접속 가능한지 테스트) : `$ nc -z 123.456.789.101 3306`
    - 2. Listening 서버를 로컬에 띄우기 : $ `nc -l [포트]`
    - 예시 : $ `nc -l 4321`
* 참고 사이트 : [리눅스에서 현재 열려 있는 포트를 확인하는 방법](https://khie74.tistory.com/1169521441)

### ngrok
* 설명 : 외부망에서 tcp 접속할 수 있도록 지정 포트를 ngrok에서 제공해주는 도메인 및 포트에 바인딩해준다.
* 공식 사이트 : [링크](https://ngrok.com)
* 주의 : 회원가입 필요(무료)
* 사용법 : `ngrok tcp [지정 포트]`
* 예시 : $ `ngrok tcp 4321`

### nslookup
* 설명 : 특정 도메인에 맵핑된 IP 확인
* 사용법 : `nslookup [도메인]`
* 예시 : $ `nslookup 0.tcp.ngrok.io`

### netstat
* 설명 : 리눅스 서버에 현재 열려 있는 포트 확인
* n:host명으로 표시 안함
* a:모든소켓 표시
* p:프로세스ID와 프로그램명 표시
* 사용법 : `netstat -anp`
* 예시(LISTEN중인 프로세스만 표시) : $ `netstat -anp | grep LISTEN`
* 참고 사이트 : [리눅스에서 현재 열려 있는 포트를 확인하는 방법](https://khie74.tistory.com/1169521441)

### iftop
* 설명 : 서버의 실시간 트래픽 확인
* 설치 
```
centOS 버전확인 -
# grep . /etc/*-release

centOS 6 -
# wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm;rpm -Uvh epel-release-6-8.noarch.rpm
# yum install iftop

centOS 7 -
# yum install epel-release;yum install iftop
```
* 사용법 참고 : https://ash84.net/2017/11/16/iftop-show-traffic/

### traceroute
* 설명 : 서버 접속 가능 및 경로 파악
* 사용법 : `traceroute -p [포트] [서버 IP] -T`

### 탐색 관려 명령어
* [리눅스 grep 명령어 사용법. (Linux grep command) - 리눅스 문자열 검색](https://recipes4dev.tistory.com/157)
* [리눅스 awk 명령어 사용법. (Linux awk command) - 리눅스 파일 텍스트 데이터 검사, 조작, 출력.](https://recipes4dev.tistory.com/171?category=768818)
* [리눅스(Linux) sort/uniq 명령어](https://websecurity.tistory.com/80)

### 파일 관리
* 90일 지난 로그 삭제
```bash
$ cd /data/project/logs
$ find . -ctime +90 -exec rm -rf {} \;
```

### ls 옵션
옵션 | 설명
----|----
-S | 파일의 크기순으로 출력된다.
-h | 용량 간소표시(K, M, G, T 등으로)
-r | 파일 및 디렉터리의 순서를 역순(reverse) 출력한다.
* $ `ll -hSr`

### dmidecode
* 설명 : 서버 제조사 확인
* 사용법 및 예시 : $ `dmidecode | more`

### hosts 파일 위치
* `/etc/hosts`
