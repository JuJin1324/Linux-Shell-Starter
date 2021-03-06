# Linux-Shell-Starter
리눅스에서 사용하는 쉘(Shell) 명령어 관련 팁 정리

## 리눅스 관련 정보 확인하기
### 현재 사용하는 쉘 확인
* $ `echo $0`

### syslog 기록되는 로그 파일 위치
* /var/log/messages

### dmidecode
* 설명 : 서버 제조사 확인
* 사용법 및 예시 : `dmidecode | more`

### hosts 파일 위치
* `/etc/hosts`

## 탐색 관련 명령어
* [리눅스 grep 명령어 사용법. (Linux grep command) - 리눅스 문자열 검색](https://recipes4dev.tistory.com/157)
* [리눅스 awk 명령어 사용법. (Linux awk command) - 리눅스 파일 텍스트 데이터 검사, 조작, 출력.](https://recipes4dev.tistory.com/171?category=768818)
* [리눅스(Linux) sort/uniq 명령어](https://websecurity.tistory.com/80)

### ls 옵션
옵션 | 설명
----|----
-S | 파일의 크기순으로 출력된다.
-h | 용량 간소표시(K, M, G, T 등으로)
-r | 파일 및 디렉터리의 순서를 역순(reverse) 출력한다.
* `ll -hSr`

## 파일 관련 명령어
* 90일 지난 로그 삭제
```bash
$ cd /data/project/logs
$ find . -ctime +90 -exec rm -rf {} \;
```

## 프로세스
### 백그라운드 프로세스
* 현재 포그라운드로 실행중인 프로세스 일시정지 : `ctrl + z` 
* 일시정지한 프로세스 목록 보기 : `jobs` 
* 가장 최근 멈춘 프로그램 재시작 : `fg`  
* jobs에서 보이는 목록 앞에 숫자에 해당하는 프로그램 시작 : `fg %[숫자]` 
* 숫자에 해당하는 프로세스 종료 : `kill %[숫자]` 

## 서비스(Daemon)
### 서비스(Daemon) 시작 / 종료
* 서비스(데몬)으로 프로그램 시작 : `service [프로그램 이름] start`
* 서비스(데몬) 프로그램 종료 : `service [프로그램 이름] stop` 

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

### 부팅시 자동 시작되는 서비스
* 실행 스크립트 생성
    - /etc/init.d 디렉토리 밑에 실행 스크립트 생성 
* update-rc.d 로 서비스 등록
    - 사용법 : `update-rc.d [-n | -f] <스크립트명> [remove | defaults] <실행순서>`
    - 서비스 등록 예시) <b>/etc/init.d</b> 아래 <b>start_myProgram</b> 실행 파일 있고 실행순서(우선순위)를 99로 원한다고 가정
    - `update-rc.d start_myProgram defaults 99`
* 서비스 삭제 예시)
    - `update-rc.d -f start_myProgram remove`
* 서비스 등록 확인 예시)
    - CLI 서비스는 <b>/etc/rc3.d</b>에서 확인, GUI 서비스는 <b>/etc/rc5.d</b>에서 확인
    - `ls /etc/rc3.d` 명령을 통해서 <b>S99start_myProgram</b> 있는지 확인
* 참조 사이트 : [링크](https://wiki.debianusers.or.kr/index.php?title=Update-rc.d)
* 주의 사항 : 서비스 실행시에 실행 디렉토리는 루트(/)로 지정됨으로 해당 실행파일과 상대경로로 연결된 파일이 있을 경우 정상 동작하지 않을 수 있다.
