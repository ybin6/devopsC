# 4주차 내용정리

## 수업 목표
* 절대 경로와 상대 경로 구분, 안전하게 경로 사용
* 'grep', 'find', '|'를 이용해 필요한 파일 내용 찾을 수 있음
* 변수, 인자, 조건, 반복 이용해 반복 작업을 자동화하는 셸 스크립트 작성할 수 있음
* 프로그램, 프로세스 구분하고 PID 이용해 프로세스 확인하고 종료할 수 있다.
* 변경내용 확인하고 브랜치->PR->Conflict해결->Merge 협업 흐름 수행할 수 있음
* IP 주소와 포트 역할 설명할 수 있음
* DNS 역할 설명할 수 있음
* ip addr, ping, curl, dig, ss로 네트워크 연결 및 서비스 상태 조회할 수 있음
* 자신의 컴퓨터에 웹 서버 띄우고 브라우저와 curl 접속할 수 잇음
* 포트 충돌을 발생시키고 해결할 수 있음




## 자동화 기초
절대 경로 - /home/neary/devops/week03/qwen-web/start.sh (위에서부터)
상대 경로 - ./week03/qwen-web/start.sh (현재 위치부터)
(셸 스크립트에서 cd "$(dirname "$0")" 쓴 이유 : 스크립트 어디에서 호출하든 스크립트가 있는 위치 기준으로 실행하기 위해)

파일찾기
find . -type f -name "*.sh" -> 셸 스크립트 파일 찾기
grep -R -n "exit 1" week03 2>/dev/null -> exit 1 포함하는 파일 찾기, -R 하위 디렉터까지 모두 검색, -n 줄 번호 표시
grep -R "exit 1" week03 2>/dev/null | wc -l -> exit 1 포함하는 파일 개수 보기, wc -l (word count, lines) 입력된 텍스트의 줄 수 계산

로그 내용 필터해서 확인(파이프와 리다이렉션)
cat server.log | grep ERROR -> cat 결과를 grep에게 전달, grep ERROR server.log 동일한 결과
cat server.log | grep ERROR | wc -l -> 위의 결과 wc -l로 확인
ls -l /etc | grep conf | head -5 -> 파이프 2번 사용한 예제
(저장) grep ERROR server.log > errors.txt -> 파일로 저장

변수와 명령 치환
cat > var.sh << 'EOF'
#!/bin/bash
USERNAME="이예빈" -> 공백 있으면 X
TODAY=$(date +%Y%m%d)
echo "이름 : $USERNAME" -> $ 없으면 문자열 그대로 출력
echo "파일명 : backup_${TODAY}.tar.gz"
EOF
chmod u+x var.sh && ./var.sh

셸 스크립트에서 인자 입력 여부 확인
```
cat > greet.sh << "EOF"
#!/bin/bash
if [ $# -eq 0 ]; then -> 주의 [] 주변에 공백 필요
	echo "사용법: $0 [이름]"
	exit 1
fi
echo "안녕하세요, $1님! (인자 개수: $#)" -> ${1}도 사용 가능
EOF

chmod u+x greet.sh
./greet.dh -> 사용법 출력
echo $? -> 1
./greet.sh 이예빈 -> 안녕하세요, 이예빈님! (인자 개수: 1) 출력
echo $? -> 0
```


조건 파일 검사
```
cat > check.sh << 'EOF'
#!/bin/bash
FILE="$1"
if [ -z "$FILE" ]; then
	echo "파일명 입력하세요"; exit 1
fi
if [ -f "$FILE" ]; then
	echo "파일"
elif [ -d "$FILE" ]; then
	echo "디렉터리"
else
	echo "없음"; exit 1
fi
EOF

chmod u+x check.sh
./check.sh var.sh -> 파일
./check.sh /etc -> 디렉터리
./check.sh 없는파일 -> 없음
```

현재 디렉터리의 .sh 파일 이름 출력
for f in *.sh; do echo "스크립트: $f"; done
var.sh
greet.sh
check.sh



## 프로세스와 백업
패키지 관리
suto apt install -y tree jq htop


프로그램 - 디스크에 저장된 실행 코드
프로세스 - 실행 중인 프로그램

ps aux | head -5 -> 프로세스 정보 앞 5줄
ps aux | grep bash -> bash 포함하는 프로세스
htop -> 실행 중인 프로세스, CPU, 메모리 사용량을 실시간으로 확인하는 도구 (종료 q)

```
sleep 300 & -> &백그라운드 (화면에 <PID>표시)
jobs -> 현재 셸의 실행 작업
ps aux : grep sleep -> PID 확인
kill <PID> -> 정상 종료 요청
ps aux | grep sleep -> 사라진 것 확인

sleep 300 &
kill -9 <PID> -> 강제 종료 - 최후의 수단
```

백업하기
1) 원본확인(if통해)
2) 백업 폴더 준비
3) 압축
4) 결과 확인
5) 목록

백업 복원 확인
```
./backup.sh test - 백업
ls backup/*.tar.gz - 파일 확인
ARCHIVE="backup/방금생성된파일명.tar.gz" - 위 목록에서 복붙 
tar -tzf "$ARCHIVE" - 내용 확인
mkdir -p restore-check - 복원 확인용 디렉터리 만들기
tar -xzf "$ARCHTVE" -C restore-check - 백업 압축 풀기
diff -rq test restore-check/test - 비교
echo $? - 0이면 원본과 복원본이 같음
```


tar - 여러 파일을 하나의 아카이브로 묶는 도구
* -tzf 내용 확인
* -xzf 압축 해제
* -czf 압축 생성
* -C change directory

diff -rq
* 다르면 A and B differ 등으로 표시됨


.gitignore 백업 결과물 제외
- 지정한 파일을 새로 Git 추적 대상에 포함하지 않도록 함
- 이미 Git 추적 중이라면, 추가해도 자동으로 제외되지 않음
```
cat >> .gitignore << 'EOF'
제외할 것들 작성
EOF
git add .gitignore
```





## Git 협업
* 실패해도 안전한 공간 있음 (브랜치)
* 합치기 전에 누군가 봐줘야 함 Pull Request (코드 리뷰)
* 컴퓨터가 자동으로 검사 해줌 (CI)

브랜치 커밋
git add .
git commit -m "커밋메시지"
git push -u origin feature/backup-log

PR 올리기
```
gh pr create --title "pr메시지" \ (밑에 계속 추가)
	-- body "설명"
	-- base main
	-- head 브랜치 (생략가능)
gh pr view --web
```
Merge(합치기)
gh pr merge --squash --delete-branch -> main에 합치면서 삭제
git switch main -> ㅡmain 브랜치로 이동
git pull --ff-only -> 바뀐 내용 pull 하기 (fast-forward만 허용)
git log --oneline -3 -> 최근 3개 커밋 보기


Conflict, Merge (충돌과 병합)
충돌 - 공통 조상 A 기준 B C 비교했을 떄, 같은 줄이 서로 다르게 수정되어 Conflict 발생. 즉, Git이 B C중 하나 임의 선택X
충돌 상태에서 merge 시도 시, 실패

해결 방법
-> 충돌표시 제거 후 최종 코드 정상적인 상태가 되도록 편집
-> Merge 가능

흐름
해결 -> add -> commit -> push -> PR업뎃 -> gh pr merge






# 네트워크


## 네트워크 기본 개념
* IP 주소 - 네트워크에서 장치 식별하기 위한 주소 
* 공인IP - 인터넷 라우팅 가능 주소
* 사설 IP - 해당 사설 네트워크 내부에서 사용 주소
* 127.0.0.1=localhost=나자신 - 네트워크 통해 다른 컴퓨터 찾아가는 주소x, 내 컴퓨터 자신 가리키는 주소


NAT
: 가정/학교 내부에서는 사설 IP 사용, 공유기/게이트웨이가 내부의 사설IP 공인IP와 매핑해 인터넷과 통신

IP/포트
: IP가 컴퓨터를 찾는 주소, 포트는 컴퓨터에서 제공하는 서비스 구분 번호



## 이름과 주소로 서버찾기
DNS
: 도메인 이름에 대응하는 IP 주소 찾는 역할 (도메인 이름 -> IP주소, 전화번호부 역할/이름 변환)

내IP 확인
ip addr -> Ubuntu / WSL2
ip addr show eth0 -> 특정 인터페이스
hostname -I -> 사설 IP 확인
curl ifconfig.me -> 공인 IP (공통)

ping : ICMP 이용해 상대 장치의 응답 여부 확인



## 서버와의 통신 확인
curl https://google.com -> HTML본문 확인
-I : --head, HEAD 요청 보내 응답 헤더 확인
-S : --silent, 진행률 또는 불필요한 메시지 숨김
-O : --output, 결과를 화면 대신 파일로 저장

응답코드
200 성공
400 잘못된 요청
404 리소스 없음
500 서버에러
503 서비스 불가


## 서버 실행 및 문제 해결
웹 서버 띄우기
python3 -m http.server 8080

127.0.0.1 - localhost, 내 컴퓨터 자신에게만 연결
0.0.0.0 - 모든 네트워크 인터페이스에서 연결 받겠다

다른 방법으로 접속
curl http://localhost:8080 -> HTML 확인
curl -I http://localhostL8080 -> 응답 헤더 및 상태 코드 확인
ss -tlnp | grep 8080 -> LISTEN 포트 확인

포트 충돌
- 기존 서버 켜둔 채(터미널1) 새 터미널(터미널2)에서 같은 포트 서버 띄우면 충돌 발생

포트 충돌 해결 방법
* 다른 포트 사용
* kill 명령으로 프로세스 종료

방화벽
* 서버가 포트 열어도 방화벽에서 해당 포트 차단하면 외부에서 접근 불가
* 서버 프로그램이 8080 포트 listen 상태여도 방화벽에서 8080접근 차단하면 외부에서 접속 불가능

