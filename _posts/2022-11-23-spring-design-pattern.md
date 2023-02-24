---
title:  "스프링이 사랑한 디자인 패턴(어댑터 패턴, 프록시 패턴, 데코레이터 패턴, 싱글턴 패턴)"
excerpt: "스프링 입문을 위한 자바 객체 지향의 원리와 이해 책 리뷰(6장)"

categories:
  - Book
tags:
  - [Java, Spring, OOP]

toc: true
toc_sticky: true
 
date: 2022-11-23
last_modified_at: 2022-11-23
---

요리와 OOP를 비유했을 때

| 요리 | 객체 지향 프로그래밍(OOP) |
|---|---|
| 요리도구| 4대 원칙(캡슐화, 상속, 추상화, 다형성) |
| 요리도구 사용법 | 설계 원칙(SOLID) |
| 레시피 | 디자인 패턴 |

디자인 패턴은 객체 지향의 특성 중 상속(extends), 인터페이스(interface/implements), 합성(객체를 속성으로 사용)을 이용한다. 


## 어댑터 패턴(Adapter Pattern)
"호출당하는 쪽의 메서드를 호출하는 쪽의 코드에 대응하도록 중간에 변환기를 통해 호출하는 패턴"


어댑터를 번역하면 변환기(converter)라고 할 수 있다. 변환기의 역할은 서로 다른 두 인터페이스 사이에 통신이 가능하게 하는 것이다.

다양한 데이터베이스 시스템을 공통의 인터페이스인 ODBC 또는 JDBC를 이용해 조작할 수 있다. 바로 ODBC/JDBC가 어댑터 패턴을 이용해 다양한 데이터베이스 시스템을 단일한 인터페이스로 조작할 수 있게 해주기 때문이다.

자바의 JRE 또한 어댑터 패턴이라고 할 수 있다.

### 어댑터 패턴 적용 전
```java
public class serviceA{
    void runServiceA(){
        System.out.println("ServiceA");
    } 
}
```
```java
public class ServiceB{
    void runServiceB(){
        System.out.println("ServiceB");
    }
}
```
```java
public class ClientWithNoAdapter{
    public static void main(String[] args){
        ServiceA sa1 = new ServiceA();
        ServiceB sb2 = new ServiceB();

        sa1.runServiceA();
        sb1.runServiceB();
    }
}
```
### 어댑터 패턴 적용 후
```java
public class AdapterServiceA{
    ServiceA sa1 = new ServiceA();

    void runService(){
        sa1.runServiceA();
    }
}
```
```java
public class AdapterServiceB{
    ServiceB sb1 = new ServiceB();

    void runService(){
        sb1.runSeviceB();
    }
}
```
기존의 ServiceA 와 serviceB의 메서드를 runService()라고 하는 같은 이름의 메서드로 호출해서 사용할 수 있게 해주는 변환기다. 
```java
public class ClientWithAdapter{
    public static void main(String[] args){
        AdapterServiceA asa1 = new AdapterServiceA();
        AdapterServiceB asb2 = new AdapberServiceB();

        asa1.runService();
        asa2.runService();
    }
}
```
클라이언트(ClientWithAdapter)가 변환기를 통해 runService()라는 동일한 메서드명으로 두 객체의 메서드를 호출하는 것을 볼 수 있다.

어댑터 패턴은 합성, 즉 객체를 속성으로 만들어서 참조하는 디자인 패턴이다.


## 프로시 패턴(Proxy Pattern)
"제어 흐름을 조절하기 위한 목적으로 중간에 대리자를 두는 패턴"

프록시는 대리자, 대변인이라는 뜻을 가진 단어다.

### 프록시 패턴 적용 전
```java
public class Service{
    public String runSomething(){
        return "서비스";
    }
}
```
```java
public class ClientWithNoProxy{
    public static void main(String[] args){
        //프록시를 이용하지 않은 호출
        Service service = new Service();
        System.out.println(service.runSomething());
    }
}
```
ClientWithNoProxy가 Service 객체의 runSomething() 메서드를 직접 호출하고 있다.

### 프록시 패턴 적용 후

```java
public interface IService{
    String runSomething();
}
```
```java
public class Service implements IService{
    public String runSomething(){
        return "서비스";
    }
}
```
```java
public class Proxy implements IService{
    IService service1;

    public String runSomething(){
        System.out.println("호출에 대한 흐름 제어가 주목적, 반환 결과를 그대로 전달");

        service1 = new Service();
        return service1.runSomething();
    }
}
```
```java
public class ClientWithProxy{
    public static void main(String[] args){
        //프록시를 이용한 호출
        IService proxy = new Proxy();
        System.out.println(proxy.runSomething());
    }
}
```
프록시 패턴의 중요 포인트
- 대리자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이때 인터페이스를 사용한다.
- 대리자는 실제 서비스에 대한 참조 변수를 갖는다(합성).
- 대리자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고 그 값을 클라이언트에게 돌려준다.
- 대리자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수도 있다.

프록시 패턴은 실제 서비스 메서드의 반환값에 가감하는 것을 목적으로 하지 않고 제어의 흐름을 변경하거나 다른 로직을 수행하기 위해 사용한다.

프록시 패턴은 개방 폐쇄 원칙과 의존 역전 원칙이 적용된 설계 패턴이다.

## 데코레이터 패턴(Decorator Pattern)
"메서드 호출의 반환값에 변화를 주기 위해 중간에 장식자를 두는 패턴" 

데코레이터는 도장/도배업자를 의미한다. 데코레이터 패턴은 프록시 패턴과 구현 방법이 같다. 다만 프록시 패턴은 클라이언트가 최종적으로 돌려 받는 반환값을 조작하지 않고 그대로 전달하는 반면 데코레이터 패턴은 클라이언트가 받는 반환값에 장식을 덧입힌다. 

- 프록시 패턴: 제어의 흐름을 변경하거나 별도의 로직 처리를 목적으로 한다. 클라이언트가 받는 반환 값을 특별한 경우가 아니면 변경하지 않는다.
- 데코레이터 패턴: 클라이언트가 받는 반환값에 장식을 더한다.

```java
public interface IService{
    public abstract String runSomething();
}
```
```java
public class Service implements IService{
    public String runSomething(){
        return "서비스";
    }
}
```
```java
public class Decorator implements IService{
    IService service;

    public String runSomething(){
        System.out.println("호출에 대한 장식 주목적, 클라이언트에게 반환 결과에 장식을 더하여 전달");
        
        service = new Service();
        return "정말" + service.runSomethging();
    }
}
```
데코레이터 패턴의 중요 포인트
- 장식자는 실제 서비스와 같은 이름의 메서드를 구현한다. 이떄 인터페이스를 사용한다.
- 장식자는 실제 서비스에 대한 참조 변수를 갖는다(합성).
- 장식자는 실제 서비스의 같은 이름을 가진 메서드를 호출하고, 그 반환값에 장식ㅇ르 더해 클라이언트에게 돌려준다.
- 장식자는 실제 서비스의 메서드 호출 전후에 별도의 로직을 수행할 수도 있다.

장식자라는 이름에서 느껴지듯 실제 서비스의 반환 값을 예쁘게 포장하는 패턴이 데코레이터 패턴임을 기억하자.

데코레이터 패턴이 프록시 패턴과 동일한 구조를 갖기에 데코레이터 패턴도 개방 폐쇄 원칙과 의존 역전 원칙이 적용된 설계 패턴이다.


## 싱글턴 패턴(Singleton Pattern)
"클래스의 인스턴스, 즉 객체를 하나만 만들어 사용하는 패턴"

싱글턴 패턴이란 인스턴스를 하나만 만들어 사용하기 위한 패턴이다. 커넥션 풀, 스레드 풀, 디바이스 설정 객체 등과 같은 경우 인스턴스를 여러 개 만들게 되면 불필요한 자원을 사용하게 되고, 또 프로그램이 예상치 못한 결과를 낳을 수 있다. 싱글턴 패턴은 오직 인스턴스를 하나만 만들고 그것을 계속해서 재사용한다.

싱글턴 패턴을 적용할 경우 의미상 두 개의 객체가 존재할 수 없다. 이를 구현하려면 객체 생성을 위한 new에 제약을 걸어야 하고, 만들어진 단일 객체를 반환할 수 있는 메서드가 필요하다. 따라서 필요한 요소를 생각해 보면 다음 세 가지가 반드시 필요하다.
- new를 실행할 수 없도록 생성자에 private 접근 제어자를 지정한다.
- 유일한 단일 객체를 반환할 수 있는 정적 메서드가 필요하다.
- 유일한 단일 객체를 참조할 정적 참조 변수가 필요하다.

```java
public class Singleton{
    static Singleton singletonObject;  //정적 참조 변수

    private Singleton(){}; //private 생성자

    //객체 반환 정적 메서드
    public static Singleton getInstance(){
        if (singletonObject == null){
            singletonObject = new Singleton();
        }
        return singletonObject;
    }
}
```
싱글턴 패턴의 특징
- private 생성자를 갖는다.
- 단일 객체 참조 변수를 정적 속성으로 갖는다.
- 단일 객체 참조 변수가 참조하는 단일 객체를 반환하는 getInstance() 정적 메서드를 갖는다.
- 단일 객체는 쓰기 가능한 속성을 갖지 않는 것이 정석이다.
