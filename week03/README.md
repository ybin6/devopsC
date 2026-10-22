# 3주차 내용정리

## 목표
* 반복적으로 사용하는 명령어를 셸 스크립트로 작성하고 실행한다.
* 셸 스크립트의 권한, shebang, 경로 처리 방식을 이해한다.
* 환경 변수와 조건문을 활용하여 실행 환경을 설정하고 오류 상황에 대응한다.
* Qwen과 Ollama 기반 로컬 AI 서비스를 실행하며 자동화의 필요성과 활용 방법을 이해한다.


## 배운 내용
* 셸 스크립트와 자동화의 필요성 알기
* 파이썬 웹 프로그램을 이용한 로컬 AI 서비스 실행
	* Qwen 모델 다운로드 및 Ollama 서버 실행
* 셸 스크립트 작성 및 실행
	* 환경 변수 설정
	* 조건문 이용한 실행 환경 점검


## 셸 스크립트 사용 이유 - 자동화
* 직접 하면 오래걸림-> 자동화 할 시 몇초만에 처리 가능
* 사람이라면 실수가 나옴 -> 자동화 할 시 실수 없이 똑같이 작업 가능


## Qwen
* 알리바바 클라우드(Alibaba Cloud)에서 개발한 대형 언어 모델
* 다양한 모델 라인업
	*  텍스트 생성, 추론, 이미지, 비디오, 오디오 통합 처리 등

설치
ollama pull qwen3:0.6b


## Ollama
* 로컬 환경에서 대규모 언어 모델(LLM)을 쉽게 설치 및 실행할 수 있게 해주는 오픈소스 플랫폼
 
* 구조
브라우저 -> Python 웹 앱 -> Ollama 서버 -> qwen3:0.6b

- 모델은 Ollama가 따로 보관, 이 폴더에 복사하거나 GitHub에 푸시x
- chat.py 파이썬 표준 라이브러리만 사용, Flask, Streamlit 설치나 가상 환경 준비 필요x
- 개인 PC에서 동작을 확인하기 위한 실습용, 외부 공개 서비스용 구성 아님

설치
sudo apt install -y python3 curl zstd
curl -fsSL https://ollama.com.install.sh | sh
(파워셸 아닌 Ubuntu 터미널에서 실행)


사전 응답 점검
curl --max-time 90 -fsS http://127.0.0.1:11434/api/generate \
	-H 'Coutent-Type: application/json' \
	-d '{"model":"wqen3:0.6b","prompt":"안녕! 한 문장으로 인사해 봐!", "think":false, "stream":false,
	"options":{"num_ctx":8192, "num_predict":512}}'



파이썬 파일 실행
python3 chat.py

웹 브라우저 주소창에 아래 입력
http://localhost:8000


## b - Billion 모델이 학습한 파라미터 개수
* 파라미터가 클수록
	* 더 많은 언어 패턴, 문법 규칙 등을 저장할 여유 생김
	* 비슷한 계열과 학습 조건에서는 더 높은 성능 보임
	* 곱하고 더하는 연산량 커져 느린 응답, 더 많은 저장 공간 및 메모리 필요



##셸 스크립트

start.sh 파일 생성
nano start.sh
#!/bin/bash -> 어떤 인터프리터로 실행할지 OS에 알려주는 부분으로 파일의 첫줄에 작성
cd "$(diranme "$0")" || exit 1 -> 해당 스크립트가 있는 디렉터리로 이동, 이동 실패 시 스크립트 종료
exec python3 chat.py -> 현재 bash를 python3로 대체 (PID유지)
완료 후 Ctrl+O -> Enter -> Ctrl+X

파일 생성 및 권한 설정
cat > start_with_export.sh << 'EOF'
#!/bin/bash
cd "$(dirname "$0")" || exit 1
export MODEL="qwen3:0.6b"
exec ./start.sh
EOF
chmod u+x start_with_export.sh


## 조건문
프로그램 설치 여부 알려주는 부분 만들기
if ! command -v python3 >/dev/null 2>&1; then  -> 만약  python3 명령어를 찾을 수 없다면
   echo "Python3를 먼저 설치하세요." > &2  -> python3 먼저 설치하세요 출력을 일반 출력 아닌 오류 메시지로 출력
   exit 1  -> 오류 상태로 종료, 자동 실패 처리 되지 않음
fi



&0 -> 표준입력
&1 -> 표준출력
&2 -> 표준오류 



 
