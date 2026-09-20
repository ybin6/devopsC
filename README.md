# Devops

## DevOps란? (Development + Operations)
* **개발(Dev)**: 프로그램 개발 (새로운 기능, 빠른 변경, 코드 작성)
* **운영(Ops)**: 서버 운영 및 서비스 유지 (안정적인 서비스, 장애 방지)
* **과거 문제점**: 개발자와 운영자의 목표 차이로 인한 충돌
* **핵심 목표**: 내가 만든 프로그램을 실제 서비스까지 어떻게 안전하고 빠르고 전달할 것인가?
* **라이프사이클 (자동화 및 연결)**
  ```text
  Plan -> Code -> Build -> Test -> Release -> Deploy -> Operate -> Monitor -> Plan
  ```

## DevSecOps (Devops <- Security)
* **예전흐름**: 개발 -> 테스트 -> 배포 직전 보안 검사
   이때 심각한 **보안 문제 발생? -> 다시 개발단계로**
-> 보안 검사 개발 단계부터 포함
* **방법** 
	* 라이브러리 취약점 검사
	* 코드 비밀번호 API Key 등 들어있는지 확인
	* 컨테이너 이미지에 취약점 있는지 검사
	* 테스트 통과 여부 확인
	* 문제 없으면 배포까지 모두 자동

##MLOps (Machine Learning + Operations)
* **AI 시대가 되며**
	* 일반 프러그래밍: Code -> Build -> Deploy
	* 머신 러닝: Data + Code + Model 세가지 관리
* **데이터 수집 -> 학습 -> 평가 -> 모델 배포 -> 모니터링 -> 재학습** 과정 관리
