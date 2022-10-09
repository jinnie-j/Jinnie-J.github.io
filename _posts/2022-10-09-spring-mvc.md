---
title:  "스프링 웹 MVC"
excerpt: "스프링 웹 MVC (Model, View, Controller)"

categories:
  - Spring
tags:
  - [Spring]

toc: true
toc_sticky: true
 
date: 2022-10-09
last_modified_at: 2022-10-09
---

## WEB MVC

### MVC (Model - View - Controller) 패턴
- Model: 어프리케이션의 데이터와 비즈니스 로직을 담는 객체
- View: Model의 정보를 사용자에게 표시한다.하나의 Model을 다양한 View에서 사용
- Controller: Model과 View의 중계 역할. 사용자의 요청을 받아 Model에 변경된 상태를 반영 및 응답 View 선택
- 역할에 따라 코드를 분리하여 의존관계를 줄이고, 재사용성 확대

- 요청 URL을 기준으로 업무 Controller를 선택하여 호출하고, 업무 Controller가 리턴한 정보를 기준으로 View를 선택하여 결과데이터를 client로 전달한다.
    ![dispatcher servlet](https://user-images.githubusercontent.com/62706198/194743107-48f92627-be67-4e5c-92bb-505e10adfd82.jpg)

### MVC 컴포넌트 간 흐름
- Client에서 request를 전달하면 DispatcherServlet에서 request를 받는다.
- DispatcherServlet에서 request정보와 함께 HandlerMapping를 호출하면 HandlerMapping에서 request를 처리할 Controller 정보를 알려준다.
- DispatcherServlet에서 HandlerAdapter를 통해 실제 Controller를 호출한다.
- Controller에서 비즈니스 로직을 수행하고 Model(결과데이터)과 View를 반환한다.
- ViewResolver에서 View Name을 받아 해당 View 객체를 반환한다.
- View에서 Model을 rendering하여 Model과 View 조합 결과를 전달한다.
- DispatcherServlet에서 Response를 통해 Client로 최종 결과를 전달한다.

### Dispatcher Servlet 기능
- Application Context 생성
    - web.xml에 지정된 context 설정 정보에 따라 필요한 전략을 추가 할 수 있다.
- Front Controller Pattern
    - Servlet 컨테이너에서 Http 프로토콜을 통해 들어오는 모든 요청을 가장 먼저 처리한다.
- Controller 호출
    - URL, parameter 정보, HTTP 명령 등을 참고하여 Handler Mapping을 통해 작업을 위임할 Controller를 결정한다.
-Request Parameter 전달
    - HandlerAdapter를 통해 Controller 메소드가 받을 수 있는 파라미터 (Map or Vom Primitive Type)로 변환하여 Controller에 전달한다. 
- View Object 생성
    - View Resolver를 이용하여 View Object를 생성한다.
- 결과 출력
    - View 작업을 통해 전달받은 최종 결과물을 HttpServletResponse 오브젝트에 담아 전달한다.

#### Context 생성
- Context 구성은 보통 ApplicationContext와 WebApplicationContext 두 계층으로 나뉘어 관리한다.
    - 공통 Bean과 WebMVC Bean을 분리함으로써 Web설정을 쉽게 관리할 수 있다.
- Application Context (Root WebApplication Context)
    - ContextLoaderListner를 통해 생성됨
    - 하위 Context에서 제공하는 Bean에 접근 불가
    - 공통 Bean을 설정할 수 있음
- WebApplicationContext
    - DispatcherServlet을 통해 생성됨
    - Root Context를 부모로 하는 하위의 WebApplicationContext 인스턴스를 생성함
    - WebMVC 관련 bean을 설정
    - Root Context에서 제공하는 Bean에 접근할 수 있음
    ![webapplication context](https://user-images.githubusercontent.com/62706198/194743493-fe9c230c-9d82-4248-93d0-93a1f645112a.jpg)


### Handler Mapping
- DispatcherServlet에 등록된 Handler Mapping 중에서 request를 처리할 수 있는 Controller를 리턴

### @MVC
- Handler Mapping이 적용될 Controller에 대하여 MVC Bean 설정을 적용한다.
- MVC Bean설정은 Annotation을 사용하여 정의한다.
- @MVC를 이용하여 Controller 메소드의 다양한 설정을 쉽고 간편하게 정의할 수 있다.
- 설정
    ```xml
    <context:component-scan base-package="springframe.sample">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
        <context:exclude-filter="annotation" expression="org.springframework.stereotype.Service"/>
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    ```

- Web ApplicationContext별로 @Controller 설정 추가
- ContextLoaderListner의 설정에서는 공통 설정인 (@Service, @Repository) 추가

### Controller
- 전통적 MVC 모델에서 모델(M)과 뷰(V)사이의 제어를 담당하는 컴포넌트
- 주요 기능으로 모델 생성을 위해 업무 Service 컴포넌트를 호출하고, 결과에 따라 화면 분기를 담당함
- 부가적으로 서버측 검증(server-side validation) 및 예외처리와 결과 데이터 가공 등을 수행함

- 관련  Annotation
    - @Controller: 해당 클래스가 Controller임을 나타내기 위한 Annotation
    - @RequestMapping: 요청에 대해 어떤 Controller, 어떤 메소드가 처리할지를 매핑하기 위한 Annotation
    - @PathVariable: 요청 url의 일부를 @RequestMapping의 변수로 받아오는 annotation
    - @RequestParam: Controller 메소드의 파라미터와 웹요청 파라미터를 매핑하기 위한 annotation
    - @ModelAttribute: Controller 메소드의 파라미터나 리턴값을 Model객체와 바인딩하기 위한 annotation
    - @SessionAttributes: Model 객체를 세션에 저장하고 사용하기 위한 annotation
    - @CookieValue: 쿠키 값을 가져와서 파라미터에 바인딩하기 위한 annotation

#### @RequestMapping
- URL과 Controller의 메소드가 매핑되도록 선언하는 Annotation이다.
- Method(메소드) 단위나 Type(클래스) 단위로 설정할 수 있다.
- 내부 HandlerMapping에서 요청된 URL을 확인하여 @RequestMapping의 정보와 일치되는 Handler 정보를 DispatcherServlet에게 알려준다.

#### @PathVariable
- @RequestMapping에 변수를 사용하고, 해당 url의 일부를 메소드 파라미터에 바인딩하는 기능
- @RequestMapping + @PathVariable
    - @RequestMapping에 선택적으로 접근하기 위한 방법
    - type, Path Level에 모두 적용할 수 있다.
    - Regular Expression을 사용하여 url을 구성할 수 있다.
    - @RequestMapping("/owners/{ownerId}")

#### @RequestParam
- Controller 메소드 파라미터와 웹 요청 파라미터를 바인딩 하기 위한 annotation
- Controller메소드 파라밑와 웹 요청 파라미터를 연결
    - public string setupForm(@RequestParam("petId") int petId, ModelMap model)
    - public String setupForm2(@RequestParam(value="id", required=false)int petID, ModelMap model)

#### @ModelAttribute
- Model 속성과 메소드의 파라미터를 바인딩 하는데 쓰이는 Annotation
- 메소드가 반환하는 값을 Model에 저장
- value 속성을 사용하지 않는 @ModelAttribute의 이름은 return Type 명으로 정의된다.
- 동일 Controller에 복수의 @ModelAttribute 메소드를 선언할 수 있다.
- 복수의 @ModelAttribute 메소드는 동일 Controller에서 @RequestMapping이 호출되기 전 모두 호출된다.
- Parameter Level
    - Parameter에 @ModelAttribute를 설정할 수 있다.
    - VO(Value Objetct)를 Controller메소드 파라미터로 사용하여 웹 요청 파라미터의 값이 binding된다.
    - Client로 넘어가는 Model값에 자동으로 VO 데이터가 담긴다.
    ```jsp
    <form:form commandName="inputVO">
        <form:input path="name"/>
        <form:input path="num"/>
    </form:form>
    ```
    ```java
    @RequestMapping("/getUserInputForm.do")
    public Sting getUserInputForm(@ModelAttrbute("inputVO") EmployeeVO employeeVO){
        //Bind된 파라미터를 Model에 담는다
        EmployeeVO resultVO = employeeService.selectEmployee(employeeVO);
    }
    ```
#### @BindingResult
- @ModelAttribute와 함께 사용되어, 요청 파라미터를 VO의 멤버변수로 변환할 때 에러가 발생했는지 여부를 알려준다. 
    ```java
    @RequestMapping("/retrieveEmployee.do")
    public String selectUser(@ModelAttribute EmployeeVO employeeVO, BindingResult bindingResult){
        validator.validator(employeeVO, bindingResult);
        if(bindingREsult.hasErrors()){
            //입력 파라미터 형식이 잘못된 경우 처리
        }else{
            //정상일 경우 처리
        }
    }
    ```
- Controller에서는 BindingResult의 hasErrors() 호출 결과에 따라 에러처리를 할 수 있다. Exception을 던져서 공통 에러페이지로 보내거나, 다시 입력 폼 화면으로 forward할 수 있다.
- 입력 폼으로 forward하는 경우 화면에서 <form:errors path="num"/> 태그를 사용하면 BindingResult에 담긴 에러메시지를 출력할 수 있다.
#### @SessionAttribute
- Session에 담긴 attribute를 삭제할 때는 SessionStatus의 setComplete()를 사용한다.
- @SessionAttribute는 클래스 레벨에서 선언할 수 있다.
- Model에 담긴 "emloyee"애 해당하는 값을 Session에 담는다.
    ```java
    @SessionAttribute("employee")
    class EmployeeController{
        @RequestMapping("/retrieveEmployee.do")
        public String selectEmployee(EmployeeVO employeeVO, ModelMap modelMap){
            try{
                EmployeeVO resultVO = employeeService.selectEmployee(employeeVO);
                modelMap.adAttribute("employee",resultVO);
            }
        }
    }

    ```
- SessionStatus
    - 세션 폼 처리시 해당 세션을 제거하기 위해 사용된다.
    - Controller에서 SessionStatus로 메소드 파라미터를 설정하고 setComplete()를 호출하면 @SessionAttribute로 지정했던 attribute만 session에서 삭제된다.

#### @CookieValue
- 쿠키에서 해당하는 쿠키값을 가져와서 Argument에 넣어준다.
- @CookieValue를 Parameter Level에서 설정해준다.
    ```java
    @RequestMapping("/retrieveEmployee.do")
    public String selectUser(@CookieValue("color") String color, ModelMap modelMap){
        ...
    }
    ```

### Handler(@Controller Method)와 ModelAndView
- Controller에서 처리된 결과값을 Client에 전달하기 위해서는 결과값을 담은 Model과 Client정보를 담은 View를 DispacherServlet에 전달해야 한다.
- Model과 View를 전달하는 방법은 메소드 파라미터와 리턴값의 활용에 따라 다양한 방법이 있다.
    - ModelAndView를 반환하는 방법
    - Model을 메소드 파라미터로 선언하고 View만 반환하는 방법
    - View만 반환하는 방법
    - Model만 반환하고 View값은 DispatcherServlet에서 자동으로 URL 기반으로 설정되는 방법
    - 그 밖에 다양한 메소드 파라미터와 리턴 값의 조합으로 Model과 View를 반환할 수 있다.

#### MethodParameter Resolver 동작원리
- Spring 버전에 따라 적합한 ArguementResolver를 사용하여야 한다.
- ArgumentResolver를 implements하여 확장 가능하다.
    ![methodparameter resolver](https://user-images.githubusercontent.com/62706198/194744821-42280f66-8021-4b7e-a128-63c571eedf34.jpg)

#### ModelAndView Resolver 동작원리
-
    ![modelandview resolver](https://user-images.githubusercontent.com/62706198/194744836-e09d824a-736e-4b96-b7f1-50135e6d60d1.jpg)

#### View Resolver
- Controller의 수행 결과에 따라 최종 결과 출력 View를 식별
- InternalResourceViewResolver
    - View Resolver의 default전략은 InternalResourceViewResolver로 JSP 처리를 지원
	- ModelAndView 객체로부터 리턴된 논리적인 이름(view name)을 취하고, 여기에 웹 어플리케이션 내의 템플릿의 경로에 도달하기 위한 접두어와 접미어를 붙이는 것으로 설정가능


### View
- 실제 화면 출력을 담당
- Default View인 InternalResourceView는 JSP 파일에 대한 경로를 받은 후, RequestDispatcher의 forward()나 include()를 호출하여 결과내용(html)을 화면으로 출력하는 View

#### View 확장
- xml, json, pdf, excel등 html이 아닌 데이터 형식으로 결과를 호출해야 하는 경우, Custom view를 구현하여 Bean으로 등록해서 사용한다.
- Spring의 AbstractView를 상속받아서 renderMergedOutputModel 메소드를 Override한 후 response에 원하는 데이터 형식으로 결과를 출력하는 방식으로 구현한다.

### 에러처리
- Controller 작업 중 발생한 예외를 처리하기 위한 기능 제공
- ExceptionResolver 동작 원리
    - Controller의 Exception 발생 시 DispatcherServlet에 등록된 ExceptionResolver가 호출된다.
    ![exception resolver](https://user-images.githubusercontent.com/62706198/194745161-71916471-3397-4b76-9272-5bb12404908d.jpg)