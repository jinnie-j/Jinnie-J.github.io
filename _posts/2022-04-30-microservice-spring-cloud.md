---
title:  "스프링 핵심 원리"
excerpt: "객체 지향 설계와 스프링"

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
