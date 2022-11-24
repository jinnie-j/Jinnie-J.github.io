---
title:  "스프링이 사랑한 디자인 패턴(템플릿 메서드 패턴, 팩토리 메서드 패턴, 전략 패턴, 템플릿 콜백 패턴)"
excerpt: "스프링 입문을 위한 자바 객체 지향의 원리와 이해 책 리뷰(6장)"

categories:
  - Book
tags:
  - [Java, Spring, OOP]

toc: true
toc_sticky: true
 
date: 2022-11-24
last_modified_at: 2022-11-24
---


## 템플릿 메서드 패턴(Template Method Pattern)

강아지와 고양이를 키운다고 상상해 보자.
```java
public class Dog{
    public void playWithOwner(){
        System.out.println("귀염둥이 이리온");
        system.out.println("멍멍");
        System.out.println("꼬리 살랑 살랑");
        System.out.println("잘했어");
    }
}
```
```java
public class Cat{
    public void playWithOwner(){
        System.out.println("귀염둥이 이리온");
        system.out.println("야옹야옹");
        System.out.println("꼬리 살랑 살랑");
        System.out.println("잘했어");        
    }
}
```
위의 예제에서는 코드가 중복된다. 상속을 통해 동일한 부분(중복)은 상위 클래스로, 달라지는 부분만 하위 클래스로 분할하여야 한다.

```java
public abstract class Animal{
    //템플릿 메서드
    public void playwithOwner(){
        System.out.prinlnt("귀염둥이 이리온");
        play();
        runSomething();
        System.out.prinlnt("잘했어");
    }
    //추상 메서드
    abstract void play();

    //Hook(갈고리) 메서드
    void runSomething(){
        System.out.prinltn("꼬리 살랑 살랑);
    }
}
```
```java
public class Dog Extends Animal{
    @Override
    // 추상 메서드 오버라이딩
    void play(){
        System.out.println("멍멍");
    }
    @Override
    // Hook(갈고리) 메서드 오버라이딩
    void runSomething(){
        System.out.println("멍멍 꼬리 살랑 살랑");
    }
}
```
```java
public class Cat Extends Animal{
    @Override
    //추상 메서드 오버라이딩
    void play(){
        System.out.println("야옹야옹");
    }
    @Override
    // Hook(갈고리) 메서드 오버라이딩
    void runSomething(){
        System.out.println("야옹야옹 꼬리 살랑 살랑");
    }
}
```
상위 클래스인 Animal에는 템플릿을 제공하는 playWithOwner() 메서드와 하위 클래스에게 구현을 강제하는 play() 추상 메서드, 하위 클래스가 선택적으로 오버라이딩 할 수 있는 runSomething() 메서드가 있다. 하위 클래스인 Dog와 Cat은 상위 클래스인 Animal에서 구현을 강제하고 있는 play() 추상 메서드를 반드시 구현해야 한다. runSomething() 메서드는 선택적으로 오버라이딩할 수 있다. 

이처럼 상위 클래스에 공통 로직을 수행하는 템플릿 메서드와 하위 클래스에 오버라이딩을 강제하는 추상 메서드 또는 선택 적으로 오버라이딩 할 수 있는 훅(Hook) 메서드를 두는 패턴을 템플릿 메서드 패턴이라고 한다. 

한 문장으로 "상위 클래스의 견본 메서드에서 하위 클래스가 오버라이딩한 메서드를 호출하는 패턴" 이다.

템플릿 메서드 패턴은 의존 역전 원칙을 활용하고 있다.

## 팩터리 메서드 패턴(Factory Method Pattern)
"오버라이드된 메서드가 객체를 반환하는 패턴" 

팩터리는 공장을 의미한다. 공장은 물건을 생산하는데 객체지향에서 팩터리는 객체를 생성한다. 결국 팩터리 메서드는 **객체를 생성 반환**하는 메서드를 말한다. 여기에 패턴이 붙으면 하위 클래스에서 팩터리 메서드를 오버라이딩해서 객체를 반환하게 하는 것을 의미한다. 

```java
public abstract class Animal{
    //추상 팩터리 메서드
    abstract AnimalToy getToy();
}
```
```java
//팩터리 메서드가 생성할 객체의 상위 클래스
public abstract class AnimalToy{
    abstract void identify();
}
```
```java
public class Dog extends Animal{
    //추상 팩터리 메서드 오버라이딩
    @Override
    AnimalToy getToy(){
        return new DogToy();
    }
}
```
```java
//팩터리 메서드가 생성할 객체
public class DogToy extends AnimalToy{
    public void identify(){
        System.out.println("나는 테니스 공");
    }
}
```
팩토리 메서드 패턴은 의존 역전 원칙을 활용하고 있다.

## 전략 패턴(Strategy Pattern)
전략 패턴을 구성하는 세 요소
- 전략 메서드를 가진 전략 객체
- 전략 객체를 사용하는 컨텍스트(전략 객체의 사용자/소비자)
- 전략 객체를 생성해 컨텍스트에 주입하는 클라이언트(제3자, 전략 객체의 공급자)

클라이언트는 다양한 전략 중 하나를 선택해 생성한 후 컨텍스트에 주입한다.   
군인, 무기, 보급 장교가 있을 때 무기는 전략이 되고, 군인은 컨텍스트, 보급 장교는 제3자 즉 클라이언트가 된다.

다양한 전략을 공통된 방식으로 사용하기 위한 인터페이스
```java
public interfae Strategy{
    public abstract void runStrategy();
}
```
전략(무기)
```java
public class StrategyGun implements Strategy{
    @Override
    public void runStrategy(){
        System.out.println("탕, 타당, 타다당");
    }
}
```
군인(컨텍스트)
```java
public class Soldier{
    void runContext(Strategy strategy){
        System.out.println("전투 시작");
        strategy.runStrategy();
        System.out.println("전투 종료");
    }
}
```
무기(전략)을 조달(생성)해서 군인(컨텍스트)에게 지급(주입)해 줄 보급 장교(클라이언트, 제3자)
```java
public class Client{
    public static void main(String[] args){
        Strategy strategy = null;
        Soldier rambo = new Soldier();

        //총을 람보에게 전달해서 전투를 수행하게 한다.
        strategy = new StrategyGun();
        rambo.runContext(strategy);

        //검을 람보에게 전달해서 전투를 수행하게 한다.
        strategy = new StrategySword();
        rambo.runContext(strategy);

        //활을 람보에게 전달해서 전투를 수행하게 한다.
        strategy = new StrategyBow();
        rambo.runContext(strategy);
    }
}
```
전략을 다양하게 변경하면서 컨텍스트를 실행할 수 있다. 
같은 문제의 해결책으로 상속을 이용하는 템플릿 메서드 패턴과 객체 주입을 통한 전략 패턴 중에서 선택/적용할 수 있다.

단일 상속만이 가능한 자바 언어에서는 상속이라는 제한이 있는 템플릿 메서드 패턴보다는 전략 패턴이 더 많이 활용된다.

한 문장으로 "클라이언트가 전략을 생성해 전략을 실행할 컨텍스트에 주입하는 패턴" 이다.

전략 패턴의은 개방 폐쇄 원칙과 의존 역전 원칙이 적용되었다.

## 템플릿 콜백 패턴(Template Callback Pattern - 견본/회신 패턴)
"전략을 익명 내부 클래스로 구현한 전략 패턴" 

템플릿 콜백 패턴은 전략 패턴의 변형으로, 스프링의 3대 프로그래밍 모델 중 하나인 DI(의존성 주입)에서 사용하는 특별한 형태의 전략 패턴이다. 

템플릿 콜백 패턴은 전략 패턴과 모든 것이 동일한데 전략을 익명 내부 클래스로 정의해서 사용한다는 특징이 있다. 
앞의 전략 패턴 코드를 템플릿 콜백 패턴으로 바꿔보자. 익명 내부 클래스를 사용하기 때문에 StrategyGun.java, StrategySword.java, StrategyBow.java는 필요 없다.

```java
public interface Strategy{
    public abstract void runStrategy();
}
```
```java
public class Soldier{
    void runContext(Strategy weaponSound){
        System.out.println("전투 시작");
        executeWeapon(weaponSound).runStrategy();
        System.out.println("전투 종료");
    }

    private Strategy executeWeapon(final String weaponSound){
        return new Strategy(){
            @Override 
            public void runStrategy(){
                System.out.println(weaponSound);
            }
        };
    }
}
```
스프링은 이런 형식으로 리팩터링된 템플릿 콜백 패턴을 DI에 적극 활용하고 있다. 따라서 스프링을 이해하고 활용하기 위해서는 전략 패턴과 템플릿 콜백 패턴, 리팩터링된 템플릿 콜백 패턴을 기억해야 한다.