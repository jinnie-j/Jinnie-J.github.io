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
last_modified_at: 2022-05-02
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
  

### Monolithic vs. MSA
#### Monolith Architecture
- 모든 업무 로직이 하나의 애플리케이션 형태로 패키지 되어 서비스
- 애플리케이션에서 사용하는 데이터가 한곳에 모여 참조되어 서비스되는 형태

#### MSA(Microservice)
- 함께 작동하는 작은 규모의 서비스들
- HTTP 통신을 이용하여 리소스 API에 통신할수 있는 작은 규모의 여러 서비스들의 묶음
- 비지니스 기능을 중심으로 구축되어야 하며 완전하게 자동화 된 배포 시스템을 사용하여야 한다. (CI/CD)
- 각각의 서비스들은 최소한의 중앙집중식 관리가 되어야하며 서로 다른 프로그래밍언어와 서로 다른 데이터 저장 기술을 사용할 수 있다.

  ![MSA](https://user-images.githubusercontent.com/62706198/166236833-e8245135-b24c-41de-be79-aaadb72ea71b.JPG)

### Microservice Architecture
#### Microservice의 특징
- Challenges
- Small Well Chosen Deployable Units
- Bounded Context
- RESTful
- Configuration Management
- Cloud Enabled
- Dynamic Scale Up And Scale Down
- CI/CD
- Visibility

#### Microservice Team Structure
- Two Pizza team
- Teams communication through API contracts
- Develop, test and deploy each service independently
- Consumer Driven Contract
- 커뮤니케이션 비용이 적게 발생하고 개발, 테스트, 운영, 배포 등읗 각각 독립적으로 개발할 수 있는 최소 단위
- 자그마한 기능을 가진 마이크로서비스를 수십, 수백개 개발을 하여 서로 서비스 인터페이스로서 연결해서 사용하는 구조로 전체 어플리케이션을 구성하는 것

### SOA와 MSA의 차이점
- 서비스 공유 지향점
  - SOA: 재사용을 통한 비용 절감
  - MSA: 서비스 간의 결합도를 낮추어 변화에 능동적으로 대응
  
- 기술 방식
  - SOA: 공통의 서비스를 ESB에 모아 사업 측면에서 공통 서비스 형식으로 서비스 제공
  - MSA: 각 독립된 서비스가 노출된 REST API를 사용
  
#### RESTful Web Service
- LEVEL 0
  - Expose soap web services in rest style 
  - 웹 서비스 상태로서 제공하기 위해서 url만 매핑한 형태
  - http://server/getPosts
  - http://server/deletePosts
  - http://server/doThis
- LEVEL 1
  - Expose resources with proper uri
  - 웹으로 공개하고자 하는 리소스에 대해서 의미있고 적절한 url로 표현
  - http://server/accounts
  - http://server/accounts/10
- LEVEL 2
  - Level1 + HTTP Methods
  - HTTP 메소드가 추가된 형태 (GET, POST, PUT, DELETE)
- LEVEL 3
  - Level2 + HATEOAS
  - DATA + NEXT POSSIBLE ACTIONS
  - 다음 작업에서 어떤 액션을 할 수 있는지 정보를 함께 넘겨준다
  
#### REST API를 설계할 때 고려해야 할 사항
- Consumer first - API를 사용하는 소비자 입장에서 명료하고 직관적인 API를 설계한다.
- Make best use of HTTP - HTTP의 장점을 최대한 살려서 설계한다.
- Request methods
  - GET, POST, PUT, DELETE
- Response Status - 상태에 따라 적절한 response 메시지를 반환해야 한다.
  - 200, 404, 400, 201, 401
- No secure info in URI - 비밀번호와 같은 크리티컬한 정보를 포함하면 안된다.
- Use plurals - 복수형태의 uri값을 사용한다.
  - prefer/users to/user
  - prefer/users/1 to/user/1
- User nouns for resources - 가능하면 명사 형태로 표시한다.  
- For exceptions - 일괄적인 end point를 사용한다.
  - define a consistence approach (PUT/gists/{id}/star)


### Microservice Architecture Structures
#### Service Mesh Capabilites
- 마이크로서비스 아키텍처를 적용한 시스템의 내부 통신을 말한다. 
- Service Mesh를 통해서 서비스와의 통신을 추상화 하고, 안전하고 빠르고 신뢰성있게 만들어주는 infra structure의 layer
- 추상화를 통해서 복잡한 내부 네트워크를 제어, 추적하고 내부 네트워크와 관련된 로직을 추가함으로써 안정성, 신뢰성, 탄력성, 표준성, 가시성, 보안성등을 확보할 수 있다.
  ![service mesh](https://user-images.githubusercontent.com/62706198/166242997-9f118d2f-6c15-4314-97db-14c59f0bb40a.JPG)
- MSA 인프라 -> 미들웨어
  - 프록시 역할, 인증, 권한부여, 암호화, 서비스 검색, 요청 라우팅, 로드 밸런싱
  - 자가 치유 복구 서비스
- 서비스간의 통신과 관련된 기능을 자동화
  
- MSA 기반 기술
  ![msa tech](https://user-images.githubusercontent.com/62706198/166243517-4976353d-50b1-4440-bd4b-9b14492a17a3.JPG)

### Spring Cloud
- 환경설정 관리, 서비스 검색, 회복성 처리, 라우팅, 프록시, 기타 등등의 서비스를 사용함에 있어서 필요한 분산 시스템이 빠르게 어플리케이션을 개발하는데 목적을 두고 스프링 클라우드가 만들어졌다.

- Centralized configuration management - 환경설정 관리
  - Spring Cloud Config Server
- Location transparency - 서비스의 등록과 위치정보 확인, 검색
  - Naming Server(Eureka)
- Load Distribution(Load Balancing) - 서버의 요청정보를 분산하기 위한 gateway 기능
  - Ribbon(Client Side)
  - Spring Cloud Gateway
- Easier REST Clients - 통신
  - FeignClient
- Visibility and monitoring - 시각화와 모니터링
  - Zipkin Distributed Tracing
  - Netflix API gateway
- Fault Tolerance - 장애 복구, 회복성
  - Hystrix   