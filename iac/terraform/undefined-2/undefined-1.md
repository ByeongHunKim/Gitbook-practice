# 사용 순서

{% embed url="https://malwareanalysis.tistory.com/429" %}

### 1. 테라폼 코드 작성

### 2. 테라폼 실행 준비 - terraform init

* 테라폼을 실행하기 위한 플러그인 설치가 필요
  * 테라폼이 수 많은 provider와 리소스를 지원하기 때문에 관련 기능이 내장 되어 있지 않기 때문
  * 그러므로 플러그인이 설치되어 있지 않으면 테라폼 실행이 실패
  * init 명령어가 실행되면 tf 파일에 정의한 BLOCK TYPE, LABEL을 분석해서 필요한 플러그인을 다운로드

### 3. 인프라 반영 예상 결과 확인 - terraform plan

* tf 코드를 바로 provider에 명시한 곳에 반영하기 전 위 명령어로 예상 결과를 확인
  * 새로 추가되는 리소스는 `+ 로 표시`

### 4. 반영 - terraform apply

* plan에서 나왔던 동일한 반영 내용을 보여주고 사용자 동의 화면 출력
* yes 입력 시 코드가 인프라에 반영

### 5. 적용 삭제 - terraform destroy

* 코드에 있는 인프라를 삭제하고 싶은 경우 위 명령어 실행
  * 삭제할 리소스는 `-` 로 표시&#x20;
*