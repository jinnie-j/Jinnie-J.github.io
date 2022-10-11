---
title:  "스프링(Spring)과 롬복(Lombok)의 결합"
excerpt: "RequiredArgsConstructor를 사용한 생성자 주입"

categories:
  - Spring
tags:
  - [Spring]

toc: true
toc_sticky: true
 
date: 2022-10-11
last_modified_at: 2022-10-11
---

## 스프링(Spring)과 롬복(Lombok)의 결합

### 롬복(Lombok)
- 롬복이란 어노테이션 기반으로 코드를 자동완성 해주는 라이브러리이다. 롬복을 사용하면 Getter나 Setter, 생성자 등을 자동완성 시킬 수 있어서 개발자의 생산성을 높여준다.
    ```java
    @Getter
    @Setter
    public class User{
        private String email;
        private String name;
    }
    ```

#### @RequiredArgsConstructor
- @RequiredArgsConstructor 어노테이션은 Not Null 이거나 final이 붙은 변수들에 대해 생성자를 만들어주는 기능을 제공하고 있다.
    ```java
    @RequiredArgsConstructor
    public class User{
        private final String email;
        private final String name;
    }
    /*
    public class User(String email, String name){
        this.email=email;
        this.name=name;
    } */
    ```

### 스프링(Spring)과 롬복(Lombok)의 결합, @RequiredArgsConstructor를 사용한 생성자 주입
#### 1. 스프링 프레임워크를 통한 생성자 주입
- 스프링 프레임워크는 생성자 주입을 권장하고 있는데, 일반적으로 다음과 같이 구현해야 한다.
    ```java
    @Service
    public class UserService{
        private UserRepository userRepository;
        private passwordEncoder passwordEncoder;

        @Autowired
        public UserService userService(UserRepository userRepository, PasswordEncoder passwordEncoder){
            this.userRepository = userRepository;
            this.passwordEncoder = passwordEncoder;
        }
    }
    ```

#### 2. final 키워드와의 결합
- 하지만 위와 같은 생성자 주입은 개선할 여지가 있어보인다. User Repository와 PasswordEncoder와 같은 빈들은 Spring 컨테이너가 관리하는 싱글톤 객체이기 때문에 변하지 않기 때문이다. 그래서 해당(Bean)들이 생성자를 통해 주입되는 시점에 불변성을 보장하도록 final 키워드를 붙여주는 것이 좋다.
    ```java
    @Service
    public class UserService{
        private final UserRepository userRepository;
        private final PasswordEncoder passwordEncoder;

        @Autowired
        public UserService userService(UserRepository userRepository, PasswordEncoder passwordEncoder){
            this.userRepository = userRepository;
            this.passwordEncoder = passwordEncoder;
        }
    }
    ```

#### 3. Lombok(롬복)과의 결합
- 위와 같은 생성자 주입 코드를 모든 빈마다 생성하려면 상당히 번거로울 것이다. 또한 새로운 빈을 생성자 주입으로 추가하는 과정도 번거롭다. 이때 Lombok 라이브러리는 이를 간소화할 수 있도록 도와주는데, 이 과정에서 스프링 프레임워크의 도움이 있다.
- 스프링 프레임워크는 1개 뿐인 생성자에 대해서 @Autowired가 없을 경우 @Autowired를 자동으로 인식하여 처리한다. 즉, 생성자가 1개 뿐이라면 @Autowired를 생략해도 된다는 것이다. (Spring 4.3 이후부터 제공)
    ```java
    @Service
    public class UserService{
        private final UserRepository userRepository;
        private final PasswordEncoder passwordEncoder;

        // @Autowired - 생성자가 1개라면 생략 가능
        public UserService userService(UserRepository userRepository, PasswordEncoder passwordEncoder){
            this.userRepository = userRepository;
            this.passwordEncoder = passwordEncoder;
        }
    }
    ```
- 이제 @Autowired없이 1개의 생성자만을 만들어주면 된다. 그리고 이때 Lombok 라이브러리를 사용하면 생성자 주입 코드를 간소화할 수 있다.
    ```java
    @Service
    @RequiredArgsConstructor
    public class UserService{
        private final UserRepository userRepository;
        private final PasswordEncoder passwordEncoder;
    }
    ```

- 롬복 라이브러리를 통해 생성자를 생성하면, 스프링 프레임워크는 해당 생성자에 @Autowired가 생략되었다고 인식하고, 적합한 스프링 빈들을 주입해준다. 이러한 스프링 프레임워크의 숨겨진 도움이 있었기 때문에 우리는 롬복을 통해 생성자 주입을 편리하게 이용할 수 있는 것이다.


### 생성자 주입을 사용해야 하는 이유
- 객체의 불변성 확보
- 테스트 코드의 작성
- final 키워드 작성 및 롬복과의 결합
- 순환 참조 에러 방지

#### 1. 객체의 불변성 확보
- 실제로 개발을 하다 보면 의존 관계의 변경이 필요한 상황은 거의 없다. 하지만 수정자 주입이나 일반 메소드 주입을 이용하면 불필요하게 수정의 가능성을 열어두어 유지보수성을 떨어뜨린다. 그러므로 생성자 주입을 통해 변경의 가능성을 배제하고 불변성을 보장하는 것이 좋다.

#### 2. 테스트 코드의 작성
- 테스트가 특정 프레임워크에 의존하는 것은 침투적이므로 좋지 못하다. 그러므로 가능한 순수한 자바로 테스트를 작성하는 것이 가장 좋은데, 생성자 주입이 아닌 다른 주입으로 작성된 코드는 순수한 자바 코드로 단위 테스트를 작성하는 것이 어렵다.
    ```java
    @Service
    public class UserService{
        @Autowired
        private UserRepository userRepository;
        @Autowired
        private MemberService memberService;

        public void register(String name){
            userRepistory.add(name);
        }
    }
    ```
- 위와 같은 코드에 대해 순수 자바 테스트 코드를 작성하면 다음과 같다.
    ```java
    public class UserServiceTest{
        @Test
        public void addTest(){
            UserService userService = new UserService();
            userService.register("hi");
        }
    }
    ```
- 위의 테스트코드는 스프링 위에서 동작하지 않으므로 의존관계 주입이 되지 않을 것이고, userRepistory가 null이 되어 add 호출시 NPE가 발생할 것이다. 이를 해결하기 위해 Setter를 사용하면 변경가능성을 열어두게 되는 단점을 갖게 된다.
- 반대로 테스트 코드에서 @Autowired를 사용하기 위해 스프링을 사용하면 단위 테스트가 아닐 뿐만 아니라, 컴포넌트들을 등록하고 초기화하는 시간 때문에 테스트 비용이 증가하게 된다. 그렇다고 대안으로 리플렉션을 사용하면 깨지기 쉬운 테스트가 된다.
- 반면에 생성자 주입을 사용하면 컴파일 시점에 객체를 주입받아 테스트 코드를 작성할 수 있으며, 주입하는 객체가 누락된 경우 컴파일 시점에 오류를 발견할 수 있다. 심지어 우리가 테스트를 위해 만든 Test객체를 생성자로 넣어 편리함을 얻을 수도 있다.

#### 3. final 키워드 작성 및 롬복과의 결합
- 생성자 주입을 사용하면 필드 객체에 final 키워드를 사용할 수 있으며, 컴파일 시점에 누락된 의존성을 확인할 수 있다. 반면에 다른 주입 방법들은 객체의 생성(생성자 호출) 이후에 호출되므로 final 키워드를 사용할 수 없다.
- 또한 final 키워드를 붙이면 롬복과 결합되어 코드를 간결하게 작성할 수 있다. 
   ```java
    @Service
    @RequiredArgsConstructor
    public class UserService{
        private final UserRepository userRepository;
        private final MemberService memberService;

        public void register(String name){
            userRepository.add(name);
        }
    }
    ```

#### 4. 순환 참조 에러 방지
- 생성자 주입을 사용하면 애플리케이션 구동 시점(객체의 생성 시점)에 순환 참조 에러를 에방할 수 있다. 예를 들어 다음과 같이 필드를 사용해 서로 호출하는 코드가 있다고 하자.
    ```java
    @Service
    public class UserService{
        @Autowired
        private MemberService memberService;

        @Override
        public void register(String name){
            memberService.add(name);
        }
    }
    ```
    ```java
    @Service
    public class MemberService{
        @Autowired
        private UserService userService;

        @Override
        public void register(String name){
            userService.add(name);
        }
    }
    ```
- 위의 두 메소드는 서로를 계속 호출할 것이고, 메모리에 함수의 CallStack이 계속 쌓여 StackOverflow 에러가 발생하게 된다.
- 하지만 생성자 주입을 이용하면 애플리케이션 구동 시점(객체의 생성 시점)에 에러가 발생하기 때문에 이러한 순환 참조 문제를 방지할 수 있다.

- 참고로 스프링부트 2.6부터는 순환 참조가 기본적으로 허용되지 않도록 변경되었다.


##### 참고
- https://mangkyu.tistory.com/155
- https://mangkyu.tistory.com/125