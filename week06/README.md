
# 6주차

## 목표
* 도커파일의 주요 명령어를 설명할 수 있다.
* 레이어 캐시의 원리를 이해하고, 순서의 중요성을 설명할 수 있다
* 제공된 애플리케이션을 도커파일로 이미지화하고 실행할 수 있다.
* 자신의 이미지를 공유하고 친구의 이미지를 받아와서 실행할 수 있다
* 멀티 아키텍처 빌드의 필요성을 이해하고 buildx를 사용할 수 있다



## Dockerfile - 이미지를 만드는 레시피
도커파일 순서
- Dockerfile 명령을 위에서부터 순서대로 처리하며 이미지 생성
1. 재료 선택
From python:3.12-slim
2. 작업 디렉터리 설정
WORKDIR /app
3. 내 파일을 이미지 안으로 복사할 때 사용
COPY requirements.txt .
4. 실행할 명령
RUN pip install -r requirements.txt
5. 나머지 코드도 이미지 안으로
COPY . .
6. 환경 변수 설정
`ENV APP_TITLE="InhaTC DevOps 방명록" \ THEME_COLOR="#0054A6"`
7. 포트 번호 (문서화)
EXPOSE 5000
8. 컨테이너 시작 시 실행할 명령
CMD ["python", "app.py"]

많이 헷갈리는 명령어
RUN - 이미지를 만들 때 한 번 실행
CMD - 컨테이너를 실행할 때마다 실행


ENV 환경 변수
셸 - export APP_TITLE="셸 설정"
docker run - -e APP_TITLE="이미지 설정"
Docker file - ENV APP_TITLE="실행 설정"

레이어, 캐시
빌드 단계마다 캐시를 판단한다
* RUN / COPY: 파일시스템 변경
* CMD / ENV / EXPOSE / USER 등: 이미지 설정 (메타데이터 생성)
- 한 레이어가 바뀌면, 그 아래 레이어는 전부 다시 생성

requirements.txt 먼저 복사하는 이유
: 자주 바뀌는 것을 뒤에 두변 빌드가 짧아진다.
캐시 활용하면 재빌드 시간 크게 줄일 수 있음
-> 순서 하나가 팀 전체의 속도 바꿈




## 실습
방명록 받고 실행
docker pull ghcr.io/makyraen/inhatc-devops-guestbook:v1
docker run -d -p 8080:5000 --name demo ghcr.io/makyraen/inhatc-devops-guestbook:v1

삭제 후, 환경 변수만 바꿔서 다시 실행
```
docker run -d -p 8080:5000 --name demo \
-e APP_TITLE="<본인 이름>의 방명록" \
-e THEME_COLOR="#16a34a" \
ghcr.io/makyraen/inhatc-devops-guestbook:v1
```
docker logs demo -> 시작로그 확인

--> 이미지(붕어빵 틀)는 그대로, 설정만 바깥에서 주입


좋지 않은 Dockerfile
```
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```
좋은 Dockerfile
```
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

배포 흐름
build → tag → push → (친구) pull → run
