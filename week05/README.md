5주차 - Docker

1, docker --version 정상 출력
Docker version 29.8.0, build 88096ef

2, `docker run hello-world` 실행
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

3, `web`이라는 이름으로 nginx 컨테이너 실행
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy,
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

4, `docker ps`에서 실행 상태를 확인
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                     NAMES
17717446149f   nginx     "/docker-entrypoint.…"   16 minutes ago   Up 16 minutes   0.0.0.0:8080->80/tcp, [::]:8080->80/tcp   web
ngin1
<hi>my name is nginx1</hi>

ngin2
<hi>my name is nginx2</hi>

ngin3
<hi>my name is nginx3</hi>

curl http://localhost:8091
curl http://localhost:8092
curl http://localhost:8093





내용정리
## 수업 목표
* 하이퍼바이저(VM)와 컨테이너의 차이를 그림으로 설명할 수 있다
* 이미지와 컨테이너 관계를 설명할 수 있다.
* docker run, ps, logs, exec, stop, rm, images와 주요 옵션을 사용할 수 있다
* 컨테이너를 활용하여 서로 다른 실행 환경을 만들고 확인할 수 있다.
* Docker 실습을 GitHub Issue로 정의하고, Issue-Branch-Pull Request-Merge 과정을 통해
학습 결과를 기록할 수 있다


## Docker
* 애플리케이션을 개발(Develop), 배포(Ship), 실행(Run)하기 위한 오픈 플랫폼
* 애플리케이션과 실행 환경을 **컨테이너(Container)**로 묶어서, 서로 다른 환경에서도 비교적 동일하게
실행할 수 있도록 도와주는 플랫폼

* Docker 컨테이너에는 프로그램 실행에 필요한 애플리케이션과 라이브러리, 설정 등을 함께 포함할
수 있음
* VM보다 상대적으로 가볍기 때문에 하나의 서버에서 여러 컨테이너를 효율적으로 운영 가능

* 개발 → 테스트 → 배포 환경 일관되게 만들 수 있음


구성요소
* Image - 컨테이너를 만들기 위한 읽기 전용 템플릿
* Container -이미지로부터 생성된 인스턴스
* Dockerfile - 이미지를 어떻게 만들지 작성하는 파일
* Registry - 이미지를 저장 및 공유하는 곳

설치확인
docker run hello-world 

컨테이너 격리
* namespace
* cgroup


## 실습을 통한 이해
(컨테이너 실행)
docker run -d -p 8080:80 --name web nginx

(여러컨테이너 사용)
docker run -d -p 8081:80 --name web2 nginx
docker run -d -p 8082:80 --name web3 nginx

(충돌)
docker run -d -p 8080:80 --name web4 nginx (포트충돌)
docker run -d -p 8084:80 --name web nginx (이름충돌)

(도커 명령어)
(실행 중인 컨테이너 출력)
docker ps

(모든 컨테이너 출력, 멈춘 컨테이너 포함)
docker ps -a

(내려받은 이미지 목록 보기)
docker images

(컨테이너 로그 확인)
docker logs web -> 현재까지의 로그 확인
docker logs <컨테이너 이름>

(로그 실시간 계속 확인)
docker logs -f web
로그보기 종료 Ctrl+C

(컨테이너 정지)
docker stop 컨테이너이름

(컨테이너 시작)
docker start 컨테이너이름

(삭제)
docker rm -f 컨테이너이름

* nginx 컨테이너는 삭제
* nginx 이미지는 로컬에 남아있음


## 컨테이너 안으로
(컨테이너 안에서 셸 실행)
docker exec -it web bash

(내부 파일 수정하기)
```
echo "<h1>Hi</h1>" > /usr/share/nginx/html/index.html
exit
```

삭제 후 다시 실행 -> 컨테이너 삭제하면 컨테이너의 쓰기 계층에 저장된 변경사항도 함께 사라짐
* 이미지는 붕어빵 틀
* 컨테이너는 붕어빵
-> 붕어빵에 크림 바르고, 잼 발라도 틀 바뀌지 않음




## GitHub Issue
Issue
무엇을, 왜 해야 하는지 기록하고 진행 상황 관리(오류만 적는 곳이 아님)
Docker 실습 역시 단순히 명령을 따라 치는 활동이 아니라, 완료 조건이 있는 하나의 작업

이슈 생성
GitHub 저장소 연 후 Issues 탭에 New Issue

이슈 내용 작성
제목과 본문 작성

이슈 생성 결과 확인
* 완료한 항목 체크박스로 진행 상황 표시
* 이슈 번호 자동 부여



이슈 작업 브랜치 생성

메인 최신 유지
git switch main
git pull --ff-only

변수에 이슈 번호 저장
ISSUE_NO=6

작업 브랜치 생성
git switch -c "docs/issue-${ISSUE_NO}-docker-start"
git branch



실습 결과 기록
README.md파일에 기록 남긴 후, 이슈 본문 체크박스 체크


커밋, 푸시 및 Pull Request 생성
git add ~/devops/week05/
git commit -m "5주차: Docker 실행 환경 및 nginx 확인 (#${ISSUE_NO})"
git push -u origin "docs/issue-${ISSUE_NO}-docker-start"

gh pr create --title "5주차: Docker" --body "Close #${ISSUE_NO}
`> 본문 작성`


Merge 후 Issue 종료 확인
gh pr merge --squash --delete-branch

git switch main
git pull --ff-only
gh issue view "$ISSUE_NO" --json state -q .state
