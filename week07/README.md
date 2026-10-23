# week07 Docker Compose

## 1. docker compose ps 결과
![컨테이너 상태](images/devops1.png)



## 2. scale 후 각 웹 컨테이너 호스트명이 바뀐 화면 결과
![Scale 결과](images/devops2-1.png)
![Scale 결과2](images/devops2-2.png)



## 3. down 후 up과 down -v 후 up 차이 비교
down 후 up은 방명록에 쓴 글이 유지되지만(Redis볼륨 유지),
down -v 후 up은 방명록이 초기화된다(Redis볼륨 함께 삭제).
![데이터 유지](images/devops3-1.png)
![데이터 삭제](images/devops3-2.png)



## 4. .env를 Git에 올리지 않는 이유 적어보기
.env 파일에는 redis 비밀번호와 같은 민감한 정보가 저장되어
있다. 이를 Git에 올리면 비밀번호가 외부에 노출되어 보안 상
문제가 발생할 수 있다. 따라서 .gitignore에 .env 파일 등록해
Git에 업로드되지 않도록 해야한다.









## 내용 정리

## 목표
* 여러 컨테이너를 함께 띄워야 하는 이유를 설명할 수 있다 
* 컨테이너끼리 서비스 이름으로 통신하는 원리를 설명할 수 
있다 * 볼륨의 필요성과 동작을 설명할 수 있다 * 
compose.yaml을 작성하고 docker compose 명령어를 사용할 
수 있다 * 환경 변수로 컨테이너 간 정보를 주입할 수 있다
* 도커 컴포즈 구성을 다듬을 수 있다


## 도커 컴포즈
* 컴포즈 - 여러 요소를 조합하여 하나의 완성된 것을 만들다
* 여러 컨테이너로 하나의 곡을 만드는 것
* 독립된 컨테이너들이 함께 동작하도록 구성 및 관리하는 도구

컨테이너
역할 하나만 부여

앱과 DB를 하나의 컨테이너에 넣을 때 문제점
1. 앱만 업데이트하고 싶은데, DB도 재시작
2. 앱과 DB를 독립적으로 확장하기 어려움 (예: 앱은 4개로 늘리고 DB는 1개만 두기)
3. 장애 격리와 개별 관리 어려움

compose.yaml 구조

```
services: # 띄울 컨테이너들
 web: # 서비스 이름
  build: . # Dockerfile로 빌드
  ports:
   - "8080:5000"
  environment:
   REDIS_HOST: "redis"
  depends_on:
   - redis
 redis: # 서비스 이름 (DNS 이름으로 사용)
  image: redis:7-alpine
  volumes:
    - redis-data:/data
volumes:
 redis-data: # 볼륨 선언
```

컨테이너 네트워크
REDIS_HOST: "redis" - IP가 아닌 이름을 작성
* 컴포즈가 네트워크를 만들고 서비스 이름을 DNS에 등록
	* 도커 내부 DNS를 통해 같은 네트워크에서 서비스 이름을 해석할 수 있도록 함
	* 이름이 그대로이기 때문에 IP는 바뀌어도 상관없음

YAML 주의사항
* 같은 단계의 키(key)는 같은 칸(레벨)에서 시작
* 숫자처럼 보이는 값은 따옴표로 감싸면 안전
* 들여쓰기는 스페이스만 사용
* 유효성 검사 방법 - docker compose config -q 


글 삭제 문제 해결 확인
글을 남긴 상태에서 web만 재시작
-> 새로고침 후 방명록 글 그대로 남아있음
-> 데이터가 web 컨테이너 안이 아니라 redis 컨테이너 안에 있기 때문
-> 역할을 분리 후 글 삭제 문제가 해결


.env
환경변수 설정
cd ~/devops/guestbook
echo "REDIS_PASSWORD=devops1234" > .env
echo "REDIS_PASSWORD=비밀번호" > .env.example 
echo ".env" >> ~/devops/.gitignore 

--> compose.yaml의 비밀번호 자리를 "${REDIS_PASSWORD}"로 수정

컴포즈는 같은 디렉터리의 .env를 자동으로 읽어서 빈칸을 채움

--> .gitignore로 제외


service_healthy
Redis 시작 → Healthcheck 통과 → Web 시작 → Web이 Redis에 연결 시도

depends_on만
Redis 시작 → Web 시작 → Web이 Redis에 연결 시도 → Redis 준비 중이면 실패




scale
* 원래 의미: 규모를 늘리거나 줄이다
* 도커에서의 의미: 컨테이너 인스턴스의 개수를 조절
관련용어
* scale-out (수평 확장) - 컨테이너나 서버 개수 늘리기
* scale-in (수평 축소) - 컨테이너나 서버 개수 줄이기
* scale-up (수직 확장) - CPU, 메모리 등 개별 서버의 성능 높이기
* scale-down (수직 축소) - 개별 서버의 자원 줄이기



config - 미리 검사
docker compose config --quiet && echo OK -> 문제x 종료코드 0
docker compose config | grep requirepass -A1 -> .env 값 실제로 들어갔는지


watch - 자동 반영



## 요즘 도커
도커 데스크톱의 AI 기능
: 도커 데스크톱의 AI 기능은 버전과 설정, 모델 실행 환경에 따라 사용 가능 여부가 달라질 수 있음


