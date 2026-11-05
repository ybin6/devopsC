## 1,replicas를 5로 늘리고 kubectl get pods 결과 캡처
![Pod 5개 확인](<images/replicas를 5로 늘리고 kubectl get pods.png>)


## 2,서비스를 통과한 요청에서 서로 다른 Pod hostname 기록
![서로 다른 Pod hostname 확인](<images/서로 다른 Pod hostname.png>)


## 3,Pod 하나를 강제 삭제
![Pod 삭제 전](<images/Pod 하나를 강제 삭제-1.png>)
![Pod 강제 삭제](<images/Pod 하나를 강제 삭제-2.png>)
![Pod 자동 복구](<images/Pod 하나를 강제 삭제-3.png>)


## 4,v1 → v2 롤링 업데이트 후 kubectl rollout history 결과 기록
![롤링 업데이트 기록](<images/롤링 업데이트.png>)


## 5,롤백 수행
![롤백 수행](<images/롤백 수행.png>)
