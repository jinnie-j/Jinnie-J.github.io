---
title:  "스프링 핵심 요소"
excerpt: "스프링 핵심 요소(Ioc, DI, AOP)"

categories:
  - Spring
tags:
  - [Spring]

toc: true
toc_sticky: true
 
date: 2022-10-09
last_modified_at: 2022-10-11
---

## 스프링 핵심 요소(Ioc, DI, AOP)

### Inversion of Controll(IoC)
- 객체 생성 및 의존 관계를 클래스 호출 방식이 아닌 외부 설정으로 관리
- 프로그램의 제어 흐름 구조가 뒤바뀌어 작업을 수행하는 쪽에서 객체를 생성

#### 클래스 호출 방식
- 클래스 내에 선언과 구현이 결합성이 높기 때문에 다양한 형태로 변화가 불가능
    ```java
    public class Car{
        private Driver driver = new Driver1();
        //private Driver driver = new Driver2();

        public void moveCar(){
            driver.drive();
        }
    }
    ```

#### IoC 방식 
- 팩토리 패턴의 장점을 더하여 결합성이 낮아진다. 실행 시점에 클래스간의 관계가 형성이 된다.
    ```xml
    <bean id="car" class="com.sample.Car">
        <property name="driver" ref="driver1">
    </bean>
    ```
    ```java
    public class Car{
        private Driver driver;

        public void setDriver(Driver driver){
            this.driver = driver;
        }
        public void moveCar(){
            driver.drive();
        }
    }
    ```

#### IoC의 장점
- 인터페이스 기반 설계가 가능
- 컴퍼넌트 재사용성 증가
- 체계적이고 효율적인 Dependency 관리

### IoC 구현

#### Dependency Injection(DI)
- 클래스 사이에 필요한 의존관계를 빈 설정 정보를 바탕으로 컨테이너가 자동으로 연결해주는 것

#### DI 방법
- 생성자 방식 Constructor Injection
    - <Construct-arg> 태그 사용
    - 의존 객체가 많아지면 사용하기 불편
- 수정자 방식 Setter Injection
    - <property> 태그 이용
    - 생성자 주입보다 유연하게 설정이 가능

#### IoC Container
- POJO이 생성, 초기화, 서비스 소멸에 관한 모든 권한을 가지면서 POJO이 생명주기를 관리
- 개발자가 직접 인스턴스를 관리하는 방식이 아닌 컨테이너로의 위임을 통해 제공하는 것이 IoC 패턴
- IoC Container의 장점
    - Transaction 처리, 인스턴스 Pooling 기능, Security와 같은 복잡한 기능들을 컨테이너를 이용함으로서 해결
    - Test의 용이성(품질 향상), 개발 생산성 향상

### Bean 기본 설정
- Bean이란 IoC Container에 의해 관리되는 객체를 말함
- <bean/> 태그를 사용하여 Bean의 생성 및 의존 관계를 설정

### Bean 주입
#### Annotation을 통한 Bean 정의 및 주입
- 주입할 Bean 클래스 생성
    ```java
   @Service(name = "employeeservice")
   public class EmployeeServiceImpl implements EmployeeService}
   //생략
   }
    ```
- 주입 받을 Bean 클래스 생성
    ```java
    @Controller
    public class EmployeeController{
        @Resource(name="employeeService")
        private EmployeeService employeeService;
        //생략
    }
    ```
- Bean 설정(context-common.xml)
    - scan 대상 패키지와 유형을 정의하며, 개별 bean에 대한 설정은 필요없음
    - component-scan: Annocation을 기반으로 Bean을 Container에 등록하고 Bean간의 주입을 수행함 
        ```xml
        <context:component-scan base-package="cevonframe.sample">
        <context:include-filter type="annotation expression="org.springframework.streotype.service">
        <context:include-filter type="annotaion" expression = "org.springframework.stereotype.Controller"/>
        </context:component-scan>
        ```
    
#### xml을 통한 Bean 설정과 Annotation을 통한 주입
- 주입할 Bean 클래스를 생성
    ```java
    public class EmployeeServiceImpl implements EmployeeService{
        //생략
    }
    ```
- 주입 받을 Bean 클래스 생성
    ```java
    public class EmployeeController{
        @Resource(name="employeeService")
        private EmployeeService employeeService;
        //생략
    }
    ```
- Bean 설정(context-common.xml)
    ```xml
    <context:annotation-config/>
    <bean id="employeeService" class="springframework.sample.hr.employee.service.EmployeeServiceImpl"/>
    <bean id="employeeController" class="springframework.sample.hr.employee.controller.EmployeeController"/>
    ```
#### xml을 통한 Bean 설정과 setter를 통한 주입
- 주입할 Bean 클래스 생성
    ```java
    public class EmployeeServiceImpl implements EmployeeService{
        //이하 생략
    }
    ```
- 주입 받을 Bean 클래스 생성
    ```java
    public class EmployeeController{
        private EmployeeService employeeService;

        public void setEmployeeService(EmployeeService employeeService){
            this.employeeService = employeeService;
        }
    }
    ```
- Bean 설정(context-common.xml)
    ```xml
    <bean id="employeeService" class="springframe.sample.hr.employee.service.EmployeeserviceImpl"/>
    <bean id="employeeController" class="springframe.sample.hr.employee.controller.EmployeeController">
        <property name="employeeService" ref="employeeService"/>
    </bean>
    ```


### Bean Lookup
#### Container를 통한 Bean 직접 접근
- 주입할 Bean 클래스 생성
    ```java
    public class EmployeeServiceImpl implements EmployeeSErvice{
        //생략
    }
    ```
- Bean 설정(context-common.xml)
    ```xml
    <bean id="employeeService" class="springrame.sample.hr.employee.service.imployeeServiceImpl">
    ```

#### GetBean() 메소드를 통한 Bean 접근
- 
    ```java
    //Container 생성
    ApplicationContext context = new ClassPathXmlApplicationContext("context-common.xml")
    //GetBean() 메소드를 통해 Bean 주입
    EmployeeService service = (EmployeeService)context.getBean("employeeService");
    ```

### Bean의 초기화 및 소멸 처리
- 인터페이스 기반 방식 - Spring에서 제공하는 InitializingBean 및 DisposalBean interface를 구현한다.
    ```java
    public class sampleBean implements InitializingBean, DisposalBean{
        public void afterPropertiesSEt() throws Exception{
            //bean 설정 setting 후 수행할 초기화 작업 기술
        }
        public void destroy() throws Exception{
            //bean 소멸시 수행할 종료 작업 기술
        }
    }
    ````
- 메소드 기반 방식 - Spring의 의존을 줄일 수 있으며, bean 설정 파일 내에 초기/소멸시 수행할 메소드를 기술한다.
    ```java
    public class SampleBean{
        public void init() throws Exception{
            //bean설정 setting 후 수행할 초기화 작업 기술
        }
        public void cleanup() throws Exception{
            //bean 소멸시 수행할 종료 작업 기술
        }
    }
    ```

### Aspect Oriented Programming(AOP)
- 다수의 객체들에 분산되어 중복적으로 존재하는 공통 관심사를 분리하는 프로그래밍 기법
- AOP는 로깅, 보안, 트랜잭션 등 공통 기능에 대해 비즈니스 로직에 영향 없이 모듈화 처리

#### AOP 장점
- 중복 코드의 제거
    - 공통 관심이 여러 모듈에 반복적으로 기술되는 현상 방지
- 비즈니스 로직의 가독성 향상
    - 핵심 기능 코드로부터 공통 관심 코드를 분리함으로서 비즈니스 로직의 가독성 향상
- 생산성 향상
    - 비즈니스 로직의 독립으로 인한 개발의 집중력을 높임
- 재사용성 향상
    - 공통 관심 코드는 여러 모듈에서 재사용될 수 있음
- 변경 용이성 증대
    - 공통 관심 코드가 하나의 모듈로 관리되기 때문에 이에 대한 변경이 수월해짐

#### AOP 구성 요소
- Advice: 각 JoinPoint에 삼입되어 동작할 수 있는 코드(동작시점: before, after, after returing, after, throwing, around 중에서 선택)
- Join Point: 모듈이 삽이보디어 동작할 수 있는 특정 위치(메소드 호출, 메소드 실행 자체, 클래스 초기화, 객체 생성 시점 등)
- Pointcut: 어느 JoinPoint를 사용할 것인지를 결정하는 선택 기능(Pattern Matching Examples, Pointcut Designators)
- Aspect: 어디에서(Pointcut) 무엇을 할 것인지(Advice)를 합쳐놓은 수행할 작업의 집합
- Weaving: Pointcut에 의해서 결정된 JoinPoint에 지정된 Advice를 삽입하는 과정 기존의 Core Concerns 모듈의 코드에 영향을 주지 않으면서 필요한 Crosscutting Concerns 기능을 추가하는 과정

#### AOP 적용 전/후 예
- 적용 전: 비즈니스 코드와 Transaction 관리 코드가 섞여 있음
    ```java
    public void insertAccount(AccountVO accountVO){
        TransactionManager tm = TransactionManagerFactory.createTransactionManger("jdbc");
        try{
            tm.begin();
            commonDao.insert("Account.insertAccount", accountVo);
            tm.commit();
        }catch(Exception e){
            tm.rollback();
        }
    }
    ```
- 적용 후: 비즈니스 코드와 Transaction 관리 코드 분리
    ```java
    public void insertAccount(AccountVO accountVO){
        commonDao.insert("Account.insertAccount", acountVO);
    }
    ```
    ```xml
    <bean id="txManager" class="org.springframework.DataSourceTransactionManager">
        <property name="dataSource", ref="dataSource"/>
    </bean>
    <aop:config>
        <aop:pointcut id="requiredTx" expression="execution(* *..*Service.insert(..))"/>
        <aop:adviser advice-ref="txAdvice" pointcut-ref="requiredTx"/>
    <aop:config>
    <tx:advice id="txAdvice" transaction-manager="txManager">
        <tx:attributes>
            <tx:method name="retrieve*" read-only="true"/>
            <tx:method name="insert*" rollback-for="Exception"/>
        </tx:attributes>
    </tx:advice>
    ```

### Advice 구현
- Before: 조인포인트(메소드 호출) 전에 수행
- After Returning: 조인포인트가 성공적으로 리턴 된 후에 동작
- After Throwing: Exception이 발생하여 조인포인트가 빠져나갈 때 수행
- After(Finally): 조인포인트를 빠져나가는 (정상적이거나 예외적인 반환) 방법에 상관없이 수행
- Around: 조인포인트 전후에 수행

- Advice 구현 예
    ```java
    public class AdviceUsingXML{
        //Service method 실행 전에 필요한 처리 작성
        public void before TargetMethod(JoinPoint thisJoinPoint){…}
        //Service method 실행 후에 필요한 처리 작성
        Public void afterTargetMethod(JoinPoint thisJoinPoint){…}
        //Service method 실행 후 리턴된 결과값으로 필요한 처리 작성
        public void afterReturningTargetMethod(JoinPoint thisJoinPoint, Object recVal){…}
        //Service method 실행 시 Exception이 발생한 경우 필요한 처리 작성
        public void afterThrowing TargetMethod(JoinPoint thisJoinPoint, Exception exception) throws Exception{…}	
        //Service Method 실행 전/후에 필요한 처리 작성
        public Object aroundTargetMethod(ProceedingJoinPoint thisJoinPoint) throws Throwable{
            //메소드 실행 전의 처리 내용
            Object retVal = thisJoinPoint.proceed();
            //메소드 실행 후의 처리 내용
            return retVal;
        }
    ```

### Spring 내부 동작
- 
    ```java
    @Service("bookService")
    public class BookServiceImpl{
        public List retrieveBookList(String title){
            //pointcut execution(* com.sample.aop.. *Impl.*(..))
            return bookDao.retrieveBookList(title);
        }
    }
    ```
- AOP 적용
    ```java
    Public class AutoGeneratedBookServiceProxy{ //스프링은 내부적으로 아래와 같은 처리흐름을 가지는 프록시를 생성
        private AdviceUsingXML advice;  // 앞에서 설정된 Advice Bean이 주입됨
        private BookServiceImpl target;   // 위에서 구현된 Proxy 대상이 되는 업무 service Bean

        public List<?> retrieveBookList(String title) throws Exception { // 실제 Service와 동일한 signature임
            advice.beforeTargetMEthod(joinPoint);
            advice.aroundTargetMethod(joinPoint);
            try{
                List<?> retVal = target.retrievBookList(title);
                advice.afterRunning TargetMethod(joinPoint, retVal);
                return retVal;
            }catch(Exception e){
                advice.afterThrowing TargetMethod(joinPoint,e);
                throw e;
            }finally{
                advice.afterTargetMethod(joinPoint);
            }
            advice.aroundTargetMethod(joinPoint);
        }
    ```

### Apache Log4J2
- 구조: Logger와 Appender를 각각 등록하고 Logger 별로 필요한 Appender를 주입함으로서 하나의 시스템에서 다양한 방식의 로그를 기록함
    - Logger: 로그의 주체(로그 파일을 작성하는 클래스)
    - Appender: 로그 출력 대상 및 방법 지원
    - Layout: Appender의 출력 포맷

- 로그 레벨
    - 설정된 로그 Level 이상의 메시지만 기록됨
    - TRACE > DEBUG > INFO > WARN > ERROR > FATAL   
