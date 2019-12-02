# Linux-terminal
리눅스 터미널에서 사용하는 명령어 관련 팁 정리

## 서비스 등록
### 실행 스크립트 생성
* /etc/init.d 디렉토리 밑에 실행 스크립트 생성 
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
* 실행스크립트 규격
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
### update-rc.d 로 서비스 등록
* update-rc.d [-n | -f] <스크립트명> [remove | defaults] <실행순서>
* 서비스 등록 예시) /etc/init.d 아래 `start_EXAMPLE` 실행 파일 있고 실행 순서를 99로 원한다고 가정
```
# update-rc.d start_EXAMPLE defaults 99
```
* 서비스 삭제 예시)
`# update-rc.d -f start_EXAMPLE remove`
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

### 네트워크 관련 명령어
* [네트워크 관련 명령어](https://github.com/JuJin1324/Linux-terminal/wiki/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EA%B4%80%EB%A0%A8-%EB%AA%85%EB%A0%B9%EC%96%B4)

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
* `$ ll -hSr`

### 서버 제조사 확인 명령어
```bash
# dmidecode | more
```

### hosts 파일 위치
* `/etc/hosts`
