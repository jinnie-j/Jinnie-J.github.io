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


### 스프링을 통한 의존성 주입 - XML 파일 사용
의사코드
- 운전자가 종합 쇼핑몰에서 타이어를 구매한다.
- 운전자가 종합 쇼핑몰에서 자동차를 구매한다.
- 운전자가 자동차에 타이어를 장착한다.

```java
자바로 표현 - 속성 메서드 사용
ApplicationContext context = new ClassPathXmlApplicationContext("expert002.xml", Driver.class);

Tire tire = (Tire)context.getBean("tire");

Car car = (Car)context.getBean("car");
car.setTire(tire);
```

생산 품목(*.java)
- Car.java
- AmericaTire.java
- KoreaTire.java

종합 쇼핑몰(expert002.xml)
```xml
판매 물품 등록(스프링 빈 설정 파일)
<bean id="tire" class="expert002.KoreaTire"></bean>
<bean id="americaTire" class="expert002.AmericaTire"></bean>
<bean id="car" class="expert002.Car"></bean>
```
운전자(Driver.java)
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

ApplicationContext context = new FileSystemXmlApplicationContext("expert002/expert002.xml");

Car car = context.getBean("car",Car.class);
Tile tire = context.getBean("tire" Tire.class);
```
KoreaTire.java가 XML 파일에서 id=tire인 bean 태그와 연결돼 있고, 다시 Driver.java의 main() 메서드 안의 코드인 contet.getBean("tire", Tire.class)와 연결돼 있는 것을 볼 수 있다.


#### 스프링을 도입해서 얻는 이득은 무엇일까?
자동차의 타이어 브랜드를 변경할 때 그 무엇도 재컴파일/재배포하지 않아도 XML 파일만 수정하면 프로그램의 실행 결과를 바꿀 수 있다.
### 스프링을 통한 의존성 주입 - 스프링 설정 파일(XML)에서 속성 주입
의사 코드 - 점점 더 현실 세계를 닮아가고 있다.
- 운전자가 종합 쇼핑몰에서 자동차를 구매 요청한다.
- 종합 쇼핑몰은 자동차를 생산한다.
- 종합 쇼핑몰은 타이어를 생산한다.
- 종합 쇼핑몰은 자동차에 타이어를 장착한다.
- 종합 쇼핑몰은 운전자에게 자동차를 전달한다.

```java
자바로 표현
ApplicationContext context = new ClassPathXmlApplicationContext("expert003/expert003.xml");
Car car = context.getBean("car", Car.class);
```
```xml
XML로 표현
<bean id="koreaTire" class="expert003.KoreaTire"></bean>
<bean id="americaTire" class="expert003.AmericaTire"></bean>
<bean id="car" class="expert003.Car">
    <property name="tire" ref="koreaTire"></property>
</bean>
```
XML 파일에 새롭게 property라고 하는 부분이 보인다. 자바에서 접ㄱ느자 및 설정자 메서드를 속성 메서드라고 하는데 영어로 속성은 property다. 결국 Driver.java에서 car.setTire (tire)라고 하던 부분을 XML파일의 property 태그를 이용해 대체하는 것이다.

Driver 파일의 car.setTire(tire) 코드가 사라지고, xml 파일에 property에서 속성이 설정되도록 하였다.

### 스프링을 통한 의존성 주입 - @Autowired를 통한 속성 주입

```java
import org.springframework.beans.factory.annotation.Autowired;

@Autowired
Tire tire;
```
import문 하나와 @Autowired 애노테이션을 이용하면 설정자 메서드를 이용하지 않고도 종합쇼핑몰인 스프링 프레임워크가 설정 파일을 통해 설정자 메서드 대신 속성을 주입해 준다.

- @Autowired는 스프링 설정 파일을 보고 자동으로 속성의 설정자 메서드에 해당하는 역할을 해주겠다는 의미다. 
- @Autowired를 통해 car의 property를 자동으로 엮어줄 수 있으므로(자동 의존성 주입) xml 파일의 property 태그의 생략이 가능하다.


#### @Autowired의 마법
기존 설정

| Car.java | expert.xml |
|--|--|
| @Autowired Tire tire; | < bean id="tire" class="expert005.AmericaTire">< /bean> |

변경 설정

| Car.java | expert.xml |
|--|--|
| @Autowired Tire tire; | < bean class="expert005.AmericaTire">< /bean> |

변경 설정에서 bean의 id 속성이 없는데도, @Autowired가 지정된 tire 속성과 expert.xml 파일에서 bean id 속성이 일치하는 것을 찾아 매칭시켰다.

- 이것이 가능한 이유는 인터페이스의 구현 여부 덕분이다. (public class AmericaTire implements Tire)
- 스프링의 @Autowired 마법은 바로 type 기준 매칭에 있다.
- 만약 같은 타입을 구현한 클래스가 여러 개 있다면 그때 bean 태그의 id로 구분해서 매칭하게 되는 것이다.
- 스프링의 @Autowired는 id와 type 중 type 구현에 우선순위가 있다.

### 스프링을 통한 의존성 주입 - @Resource를 통한 속성 주입 
- @Autowired는 스프링의 어노테이션이고, @Resource는 자바 표준 어노테이션이다.
- 스프링 프레임워크를 사용하지 않는다면 @Autowired는 사용할 수 없고 오직 @Resource만을 사용해야 한다.
- @Autowired의 경우 type과 id 가운데 매칭 우선순위는 type이 높다. 
- @Resource의 경우 type과 id 가운데 매칭 우선순위는 id가 높다. id로 매칭할 빈을 찾지 못한 경우 type으로 매칭할 빈을 찾게 된다.

### 스프링을 통한 의존성 주입 - @Autowired vs. @Resource vs. < property> 태그

실제로 @Autowired와 @Resource를 바꿔서 사용하는 데 크게 차이가 없다. 그럼 둘 중에 어느 것을 써야 할까?
- 자동으로 주입된다는 의미에서는 @Autowired가 명확해 보이지만 실제 Car 입장에서 보면 @Resource라는 표현이 더 어울린다. 
- 그리고 나중에 스프링이 아닌 다른 프레임워크로 교체되는 경우를 대비하면 자바 표준인 @Resource를 쓰는 것이 유리하다.

@Resource는 사실 < bean> 태그의 자식인 < property>태그로 해결될 수 있다. 그럼 @Resoruce vs. < property> 중에서는 누구의 손을 들어줘야 할까?
- XML 파일만 봐도 DI 관계를 손쉽게 확인할 수 있기 때문에 < property>에 손을 들어주었다.
- 그렇지만 개발 생산성은 @Resource가 더 나을 것이다. 
- 반면 < property>는 유지보수성이 좋다.
- 프로젝트의 규모가 커지면 XML 파일의 규모가 커지기 마련인데 XML 파일도 용도별로 분리할 수 있다. 

> @Autowired와 @Resource 중에서는 @Resource를 추천한다.   
@Resource와 < property> 중에서는 < property>를 추천한다.

---
### 정리
변수에 값을 할당하는 모든 곳에 의존 관계가 생긴다. 즉, 대입 연산자 (=)에 의해 변수에 값이 할당되는 순간에 의존이 생긴다. 변수가 지역 변수이건 속성이건, 할당되는 값이 리터럴이건 객체이건 의존은 발생한다. 

의존 대상이 내부에 있을 수도 있고, 외부에 있을 수도 있다. DI는 외부에 있는 의존 대상을 주입하는 것을 말한다. 의존 대상을 구현하고 배치할 때 SOLID와 응집도는 높이고 결합도는 낮추라는 기본 원칙에 충실해야 한다. 그래야 프로젝트의 구현과 유지보수가 수월해진다.

