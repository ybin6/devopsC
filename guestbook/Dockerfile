# ---------------------------------------------------------------
# 학생 과제: 아래 각 줄이 무엇을 하는지 주석으로 설명을 달아보세요.
# ---------------------------------------------------------------

FROM python:3.12-slim
#python 3.12가 설치된 이미지 기반으로 사용

WORKDIR /app
#컨테이너 내부 작업 디렉터리 /app으로 설정

COPY requirements.txt .
#라이브러리 목록 파일을 작업 디렉터리에 복사
RUN pip install --no-cache-dir -r requirements.txt
#이미지 빌드 중 필요한 라이브러리 설치
COPY . .
#현재 폴더 파일들을 이미지에 복사
RUN useradd -m appuser
#일반 사용자 계정 생성
USER appuser
#root 대신 일반 사용자로 실행하도록 설정

ENV APP_TITLE="예빈이의 방명록" \
    THEME_COLOR="#CE0E2D"
#프로그램 기본 환경 변수 설정(타이틀 설정, 컬러 설정)

EXPOSE 5000
#컨테이너가 사용할 포트 정보 명시

CMD ["python", "app.py"]
#컨테이너 시작시 파이썬 프로그램 실행
