---
title:  "Microservice와 Spring Cloud"
excerpt: "마이크로서비스 애플리케이션(MSA)"

categories:
- Microservice 
  
tags:
- [Microservice, Spring Cloud]

toc: true
toc_sticky: true

date: 2022-04-30
last_modified_at: 2022-04-30
---

## Microservice와 Spring Cloud

### Software Architecture
- Antifragile
  - Auto scaling (자동 확장성)
  - MicroServices (전체 서비스에서 구축하고 있는 개별적인 모듈이나 기능을 독립적으로 개발, 배포, 운영할 수 있도록 세분화 된 서비스)
  - Chaos engineering (시스템의 변동, 예견되지 않은 불확실성에 대해서도 안정적인 서비스를 제공하도록 구축되어야 한다.)
  - Continuous deployments (지속적인 통합, 지속적인 배포)
    
### Cloud Native Architecture
- 확장 가능한 아키텍처
  - 시스템의 수평적 확장에 유연
  - 확장된 서버로 시스템의 부하 분산, 가용성 보장
  - 시스템 또는, 서비스 애플리케이션 단위의 패키지(컨테이너 기반 패키지)
  - 모니터링
- 탄력적 아키텍처
  - 서비스 생성 - 통합 - 배포, 비즈니스 환경 변화에 대응 시간 단축
  - 분할 된 서비스 구조
  - 무상태 통신 프로토콜
  - 서비스의 추가와 삭제 자동으로 감지
  - 변경된 서비스 요청에 따라 사용자 요청 처리 (동적 처리)
- 장애 격리 (Fault isolation)
  - 특정 서비스에 오류가 발생해도 다른 서비스에 영향 주지 않음
    
### Cloud Native Application

#### CI/CD
- 지속적인 통합, CI
  - Continuous Integration
  - 통합 서버, 소스 관리(SCM), 빌드 도구, 테스트 도구
  - ex) Jenkins, Team CI, Travis CI
- 지속적 배포, CD
  - Continuous Delivery
  - Continuous Deployment
  - pipe line
    
#### DevOps
![devops](https://user-images.githubusercontent.com/62706198/166110768-7386b101-d106-4a57-8a57-de18f943a874.JPG)

#### Container 가상화
![container](https://user-images.githubusercontent.com/62706198/166111032-b0e962cd-ddad-4496-982c-4e4dded99b5b.JPG)
- 기존에 로컬 환경에서 운영하고 유지해야만 했던 시스템을 클라우드 환경으로 이전하여 적은비용으로 탄력성있는 시스템을 구축하게된 배경
- 컨테이너 가상화 위에서 작동되는 서비스 들은 가볍고 빠르게 운영할 수 있다.

### Factors
#### 12 Factors
- CodeBase (코드베이스)
  - 애플리케이션은 1개의 코드 베이스를 통해 관리되어야 하며, 동일한 코드로 운영/개발에 배포하여야 한다.
- Dependencies (종속성)
  - 애플리케이션의 모든 종속성을 명시적으로 선언하여 사용한다.
- Configurations (설정)
  - 모든 설정 정보는 코드로부터 분리된 공간에 저장되어야 하고, 런타임에서 코드에 의해 읽혀야 한다.
- Linkable Backing Services (백엔드 서비스)
  - 백엔드 서비스를 연결된 리소스로 취급한다.
- Stages of Creation (빌드, 릴리즈 실행)
  - 코드 베이스는 build, release, run 단계를 거쳐 배포로 변환되며, 각 단계는 엄격하게 분리되어야 한다.
- Stateless Processes (프로세스)
  - 실행 환경에서 앱은 하나 이상의 프로세스로 실행되며, 각 프로세스는 stateless로 아무것도 공유하지 않아야 한다.
- Port Binding (포트 바인딩)
  - 배포된 SaaS 애플리케이션을 타 애플리케이션에서 접근할 수 있도록 포트 바인딩을 통해 서비스를 공개한다. 
- Concurrency (동시성)
  - 앱은 수평으로 확장할 수 있어야 하고, Processes에 의해 동시성을 높일 수 있다. 
- Disposability (폐기 가능)
  - 프로세스는 shut down 신호를 받았을 때 graceful shut down 해야한다. 웹프로세스의 그레이스풀 셧다운 과정에서는 서비스 포트의 수신을 중지하고, 현재 처리 중인 요청이 끝나길 기다린 뒤에 프로세스가 종료된다.
- Development & Production Parity (dev/prod 일치)
  - development, staging, production 환경을 최대한 비슷하게 유지한다.
- Logs (로그)
  - 로그를 이벤트 스트림으로 취급하여 로그를 로컬에 저장하지 않는다.
- Admin Processes for eventual processes (Admin 프로세스)
  - admin/maintenance 작업을 일회성 프로세스로 실행해야 한다.