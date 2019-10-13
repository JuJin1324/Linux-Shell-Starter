# Linux-terminal
리눅스 터미널에서 사용하는 명령어 관련 팁 정리

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
