---
title:  "스프링 삼각형과 설정 정보(IoC/DI - 제어의 역전/의존성 주입)"
excerpt: "스프링 입문을 위한 자바 객체 지향의 원리와 이해 책 리뷰(7장)"

categories:
  - Book
tags:
  - [Java, Spring, OOP]

toc: true
toc_sticky: true
 
date: 2022-12-01
last_modified_at: 2022-12-01
---

스프링을 이해하는 데는 POJO(Plain Old Java Object)를 기반으로 스프링 삼각형이라는 애칭을 가진 IoC/DI, AOP, PSA라고 하는 스프링의 3대 프로그래밍 모델에 대한 이해가 필수다.

## IoC/DI - 제어의 역전/의존성 주입

### 프로그래밍에서 의존성이란?
의사 코드
- 운전자가 자동차를 생산한다.
- 자동차는 내부적으로 타이어를 생상한다.

자바로 표현
```java
new Car();
Car 객체 생성자에서 new Tire();
```
의존성을 단순하게 정의하면 다음과 같다.
- 의존성은 new다.
- new를 실행하는 Car와 Tire 사이에서 Car가 Tire에 의존한다.

결론적으로 전체가 부분에 의존한다고 표현할 수 있다. 

### 의존관계를 직접 해결하는 경우
```java
interface Tire{
    String getBrand();
}
```
```java
public class KoreaTire implements Tire{
    public String getBrand(){
        return "코리아 타이어";
    }
}
```
```java
public class AmericaTire implements Tire{
    public String getBrand(){
        return "미국 타이어";
    }
}
```
```java
public class Car{
    Tire tire;

    public Car(){
        tire = new KoreaTire();  //의존 관계가 일어나고 있는 부분
        //tire = new AmericaTire();
    }
}
```
```java
public class Driver{
    public static void main(String[] args){
        Car car = new Car();
        System.out.println(car.getTireBrand());
    }
}
```
- 자동차는 타이어에 의존한다.
- 운전자는 자동차를 사용한다.
- 운전자가 자동차에 의존한다고 봐도 된다.
- 자동차의 생성자 코드에서 tire 속성에 새로운 타이어를 생성해서 참조할 수 있게 해주었다.

### 스프링 없이 의존성 주입하기1 - 생성자를 통한 의존성 주입
의사코드
- 운전자가 타이어를 생산한다.
- 운전자가 자동차를 생산하면서 타이어를 장착한다.

자바로 표현
```java
Tire tire = new KoreaTire();
Car car = new Car(tire);
```
주입이란?
- 주입이란 말은 외부에서라는 뜻을 내포하고 있는 단어다.
결국 자동차 내부에서 타이어를 생산하는 것이 아니라 외부에서 생상된 타이어를 자동차에 장착하는 작업이 주입이다.

```java
public class Car{
    Tire tire;

    public Car(Tire tire){
        this.tire = tire;
    }
}
```
```java
public class Driver{
    public static void main(String[] args){
        Tire tire = new KoreaTire();
        //Tire tire = new AmericaTire();
        Car car = new Car(tire);
    }
}
```
이전과 다르게 new를 통해 타이어를 생성하는 부분이 Car.java에서 Driver.java로 이동했다. 그리고 생산된 tire 객체 참조 변수를 Car 생성자의 인자로 전달한다.

현실세계에 비유하자면 자동차가 생산될 때 어떤 타이어를 생산해서 장착할까를 자동차가 스스로 고민하지 않고, 운전자가 차량을 생산할 때 운전자가 어떤 타이어를 장착할까를 고민하게 하는 것이다. 자동차는 어떤 타이어를 장착할까를 더는 고민하지 않아도 된다.

### 스프링 없이 의존성 주입하기2 - 속성을 통한 의존성 주입

생성자를 통해 의존성을 주입하는 방법과 속성을 통해 의존성을 주입하는 방법 중 어느 쪽이 더 좋은가에 대한 의견이 분분했었는데, 최근에는 속성을 통한 의존성 주입보다는 생성자를 통한 의존성 주입을 선호하는 사람들이 많다.

실세계에서라면 십년 가까이 타게 되는 차를 사고 타이어를 교체하는 일이 빈번할 수 있지만 프로그램에서는 한번 주입된 의존성을 계속 사용하는 경우가 더 일반적이기 때문이다.

의사코드
- 운전자가 타이어를 생산한다.
- 운전자가 자동차를 생산한다.
- 운전자가 자동차에 타이어를 장착한다.
자바로 표현 - 속성 접근자 메서드 사용
```java
Tire tire = new KoreaTire();
Car car = new Car();
car.setTire(tire);
```

```java
public class Car{
    Tire tire;
    
    public Tire getTire(){
        return tire;
    }
    public void setTire(Tire tire){
        this.tire =tire;
    }
    public String getTireBrand(){
        return "장착된 타이어: " + tire.getBrand();
    }
}
```
생성자가 없어졌고, tire 속성에 대한 접근자 및 설정자 메서드가 생겼다.
```java
public class Driver{
    public static void main(String[] args){
        Tire tire = new KoreaTire();
        Car car = new Car();
        car.setTire(tire);
    }
}
```


